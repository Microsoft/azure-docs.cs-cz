---
title: 'Kurz: Azure Active Directory Integration s oddělením služeb SolarWinds (dříve Samanage) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a oddělením služeb SolarWinds (dříve Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ab720430af0341f3a42d9f4d4dc19b9469872211
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675592"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Kurz: Azure Active Directory integrace se službou SolarWinds Service Desk (dříve Samanage)

V tomto kurzu se dozvíte, jak integrovat SolarWinds s Azure Active Directory (Azure AD).
Integrace SolarWinds s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k SolarWinds.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k SolarWinds (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s SolarWinds Service Desk (dříve Samanage) potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Samanagem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SolarWinds podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-solarwinds-from-the-gallery"></a>Přidání SolarWinds z Galerie

Pokud chcete nakonfigurovat integraci SolarWinds do služby Azure AD, musíte přidat SolarWinds z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat SolarWinds z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory** ikona.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SolarWinds** , vyberte **SolarWinds** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![SolarWinds v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SolarWinds na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SolarWinds.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SolarWinds, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro službu SolarWinds Service Desk](#configure-solarwinds-single-sign-on)** -pro konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele pro SolarWinds Service](#create-solarwinds-test-user)** – abyste měli protějšek Britta Simon v oddělení služeb SolarWinds, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí SolarWinds, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **SolarWinds** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Samanage a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru, které jsou vysvětleny dále v tomto kurzu. Další podrobnosti získáte od [týmu podpory Samanage klientů](https://www.samanage.com/support). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení SolarWinds** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Nakonfigurovat SolarWinds Service Desk – Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu SolarWinds společnosti jako správce.

2. Klikněte na **řídicí panel** a v levém navigačním podokně vyberte **Nastavení** .
   
    ![Řídicí panel](./media/samanage-tutorial/tutorial_samanage_001.png "Řídicí panel")

3. Klikněte na **jednotné přihlašování** .
   
    ![Jednotné přihlašování](./media/samanage-tutorial/tutorial_samanage_002.png "Jednotné přihlašování")

4. Přejděte k části **přihlášení pomocí SAML** , proveďte následující kroky:
   
    ![Přihlášení pomocí SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Přihlášení pomocí SAML")
 
    a. Klikněte na **Povolit jednu Sign-On s SAML** .  
 
    b. Do textového pole **Adresa URL poskytovatele identity** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.    
 
    c. Potvrďte **přihlašovací adresu URL** , která se shoduje s **adresou URL** **základního konfiguračního oddílu SAML** v Azure Portal.
 
    d. Do textového pole **Adresa URL pro odhlášení** zadejte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
 
    e. Do textového pole **vystavitele SAML** zadejte identifikátor URI ID aplikace nastavený ve Vašem zprostředkovateli identity.
 
    f. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do pole **Vložit poskytovatele identity x. 509 pod** textovým polem.
 
    například **Pokud v SolarWinds neexistují** , klikněte na vytvořit uživatele.
 
    h. Klikněte na **Aktualizovat** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k SolarWinds.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **SolarWinds** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SolarWinds** .

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-solarwinds-test-user"></a>Vytvořit testovacího uživatele SolarWinds

Aby se uživatelé Azure AD mohli přihlásit k SolarWinds, musí se zřídit v SolarWinds.  
V případě SolarWinds je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu SolarWinds společnosti jako správce.

2. Klikněte na **řídicí panel** a vyberte **Nastavení** v levém navigačním panelu.
   
    ![Nastavení](./media/samanage-tutorial/tutorial_samanage_001.png "Nastavení")

3. Klikněte na kartu **Uživatelé** .
   
    ![Uživatelé](./media/samanage-tutorial/tutorial_samanage_006.png "Uživatelé")

4. Klikněte na **Nový uživatel** .
   
    ![Nový uživatel](./media/samanage-tutorial/tutorial_samanage_007.png "Nový uživatel")

5. Zadejte **jméno** a **e-mailovou adresu** účtu Azure Active Directory, který chcete zřídit, a klikněte na **vytvořit uživatele** .
   
    ![Vytvořit uživatele](./media/samanage-tutorial/tutorial_samanage_008.png "Vytvořit uživatele")
   
   >[!NOTE]
   >Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní. K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů SolarWinds nebo rozhraní API poskytovaná SolarWinds.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici SolarWinds, měli byste se automaticky přihlásit k SolarWinds, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)