---
title: 'Kurz: Azure Active Directory integrace s Work.com | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Work.com.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: c549e8169ad7fc7a5eb7f810d76deb33af0e5f65
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92508923"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Kurz: Azure Active Directory integrace s Work.com

V tomto kurzu se dozvíte, jak integrovat Work.com s Azure Active Directory (Azure AD).
Integrace Work.com s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Work.com.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Work.com (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Work.com potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Work.comm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Work.com podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-workcom-from-the-gallery"></a>Přidání Work.com z Galerie

Pokud chcete nakonfigurovat integraci Work.com do služby Azure AD, musíte přidat Work.com z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Work.com z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Work.com**, vyberte **Work.com** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Work.com v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Work.com na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Work.com.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Work.com, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Work.com](#configure-workcom-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Work.com test User](#create-workcom-test-user)** – pro Britta Simon v work.com, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

>[!NOTE]
>Pokud chcete nakonfigurovat jednotné přihlašování, musíte si zatím nastavit vlastní název domény Work.com. Musíte definovat aspoň název domény, otestovat název domény a nasadit ho do celé organizace.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Work.com, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Work.com** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Work.com a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení Work.com** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-workcom-single-sign-on"></a>Nakonfigurovat Work.com jednu Sign-On

1. Přihlaste se ke svému tenantovi Work.com jako správce.

2. Přejít na **Nastavení**.
   
    ![Nastavení](./media/work-com-tutorial/ic794108.png "Nastavení")

3. V levém navigačním podokně v části **Spravovat** kliknutím na **Správa domény** rozbalte související část a potom kliknutím na **moje doména** otevřete stránku **moje doména** . 
   
    ![Moje doména](./media/work-com-tutorial/ic767825.png "Moje doména")

4. Pokud chcete ověřit, jestli je vaše doména nastavená správně, ujistěte se, že je v**kroku 4 nasazená pro uživatele**, a zkontrolujte**Nastavení moje doména**.
   
    ![Doména nasazená pro uživatele](./media/work-com-tutorial/ic784377.png "Doména nasazená pro uživatele")

5. Přihlaste se ke svému tenantovi Work.com.

6. Přejít na **Nastavení**.
    
    ![Nastavení](./media/work-com-tutorial/ic794108.png "Nastavení")

7. Rozbalte nabídku **ovládací prvky zabezpečení** a pak klikněte na **Nastavení jednoho Sign-On**.
    
    ![Nastavení jednoho Sign-On](./media/work-com-tutorial/ic794113.png "Nastavení jednoho Sign-On")

8. Na stránce **Nastavení jednoho Sign-On** proveďte následující kroky:
    
    ![SAML – povoleno](./media/work-com-tutorial/ic781026.png "SAML – povoleno")
    
    a. Vyberte **povoleno SAML**.
    
    b. Klikněte na **Nový**.

9. V části **nastavení jediného Sign-On SAML** proveďte následující kroky:
    
    ![Nastavení Single Sign-On SAML](./media/work-com-tutorial/ic794114.png "Nastavení Single Sign-On SAML")
    
    a. Do textového pole **název** zadejte název konfigurace.  
       
    > [!NOTE]
    > Zadáním hodnoty pro **název** se automaticky naplní textové pole s **názvem rozhraní API** .
    
    b. Do textového pole **vystavitele** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.
    
    c. Pokud chcete stažený certifikát nahrát z Azure Portal, klikněte na **Procházet**.
    
    d. Do textového pole **ID entity** zadejte `https://salesforce-work.com` .
    
    e. Jako **typ identity SAML**vyberte **kontrolní výraz obsahuje ID federace z objektu User**.
    
    f. Jako **umístění identity SAML**, vyberte **identita je v elementu NameIdentfier příkazu Subject**.
    
    například Do textového pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    h. V poli **Adresa URL pro odhlášení zprostředkovatele identity** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
    
    i. Jako **Zprostředkovatel služby inicioval vazbu žádosti**vyberte **http post**.
    
    j. Klikněte na **Uložit**.

10. Na portálu Work.com Classic v levém navigačním podokně kliknutím na **Správa domény** rozbalte související část a potom kliknutím na **moje doména** otevřete stránku **moje doména** . 
    
    ![Moje doména](./media/work-com-tutorial/ic794115.png "Moje doména")

11. Na stránce **moje doména** v části **branding přihlašovací stránky** klikněte na **Upravit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic767826.png "Branding přihlašovací stránky")

12. Na stránce **značky přihlašovací stránky** v části **ověřovací služba** se zobrazí název vašeho **nastavení jednotného přihlašování SAML** . Vyberte ji a pak klikněte na **Uložit**.
    
    ![Branding přihlašovací stránky](./media/work-com-tutorial/ic784366.png "Branding přihlašovací stránky")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Work.com.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Work.com**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Work.com**.

    ![Odkaz Work.com v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-workcom-test-user"></a>Vytvořit testovacího uživatele Work.com

Aby se uživatelé mohli Azure Active Directory přihlásit, musí se zřídit až Work.com. V případě Work.com je zřizování ručním úkolem.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Při konfiguraci zřizování uživatelů proveďte následující kroky:

1. Přihlaste se k webu Work.com společnosti jako správce.

2. Přejít na **Nastavení**.
   
    ![Nastavení](./media/work-com-tutorial/IC794108.png "Nastavení")

3. Přejít na **Správa uživatelů \> **.
   
    ![Správa uživatelů](./media/work-com-tutorial/IC784369.png "Správa uživatelů")

4. Klikněte na **Nový uživatel**.
   
    ![Všichni uživatelé](./media/work-com-tutorial/IC794117.png "Všichni uživatelé")

5. V části pro úpravy uživatelů proveďte následující kroky, a to v atributu platného účtu Azure AD, který chcete zřídit do souvisejících textových polí:
   
    ![Úprava uživatele](./media/work-com-tutorial/ic794118.png "Úprava uživatele")
   
    a. Do textového pole **jméno a příjmení** zadejte **křestní jméno** uživatele **Britta**.
    
    b. Do textového pole **příjmení** zadejte **jméno** uživatele **Simon**.
    
    c. Do textového pole **alias** zadejte **jméno** uživatele **BrittaS**.
    
    d. Do textového pole **e-mail** zadejte **e-mailovou adresu** uživatele Brittasimon@contoso.com .
    
    e. Do textového pole **uživatelské jméno** zadejte uživatelské jméno jako uživatel Brittasimon@contoso.com .
    
    f. Do textového pole **Přezdívka jméno** zadejte **Přezdívka** uživatele **Simon**.
    
    například Vyberte **role**, **Uživatelská licence**a **profil**.
    
    h. Klikněte na **Uložit**.  
      
    > [!NOTE]
    > Držitel účtu Azure AD obdrží e-mail s odkazem na potvrzení účtu, než se aktivuje.
    > 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Work.com, měli byste se automaticky přihlásit k Work.com, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)