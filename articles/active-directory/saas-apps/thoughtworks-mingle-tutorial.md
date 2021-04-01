---
title: 'Kurz: Azure Active Directory integrace s Thoughtworks Mingle | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Thoughtworks Mingle.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c3eeb1577e628965e3e5a35fa20c072224383149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514618"
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Kurz: Azure Active Directory integrace s Thoughtworks Mingle

V tomto kurzu se dozvíte, jak integrovat Thoughtworks Mingle s Azure Active Directory (Azure AD).
Integrace Thoughtworks Mingle se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Thoughtworks Mingle.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Thoughtworks Mingle (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Thoughtworks Mingle potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Thoughtworks Mingle s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Thoughtworks Mingle podporuje jednotné přihlašování se spuštěným **SP**

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>Přidání Thoughtworks Mingle z Galerie

Pokud chcete nakonfigurovat integraci Thoughtworks Mingle do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Thoughtworks Mingle z galerie.

**Pokud chcete přidat Thoughtworks Mingle z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Thoughtworks Mingle**, vyberte **Thoughtworks Mingle** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Thoughtworks Mingle v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Thoughtworks Mingle na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Thoughtworks Mingle.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Thoughtworks Mingle, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Thoughtworks Mingle](#configure-thoughtworks-mingle-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Thoughtworks Mingle Test User](#create-thoughtworks-mingle-test-user)** – abyste měli protějšek Britta Simon v Thoughtworks Mingle, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Thoughtworks Mingle, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Mingle pro Thoughtworks** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Thoughtworks Mingle, informace o jednotném přihlašování v doméně a adresách URL](common/sp-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [tým podpory Thoughtworks Mingle pro klienta](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Mingle Thoughtworks** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-thoughtworks-mingle-single-sign-on"></a>Konfigurace Thoughtworks Mingle Single Sign-On

1. Přihlaste se k webu společnosti **Thoughtworks Mingle** jako správce.

2. Klikněte na kartu **správce** a potom klikněte na položku **Konfigurace jednotného přihlašování**.
   
    ![Karta správce](./media/thoughtworks-mingle-tutorial/ic785157.png "Konfigurace jednotného přihlašování")

3. V části **Konfigurace jednotného přihlašování** proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/thoughtworks-mingle-tutorial/ic785158.png "Konfigurace jednotného přihlašování")
    
    a. Pokud chcete nahrát soubor metadat, klikněte na **zvolit soubor**. 

    b. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Thoughtworks Mingle.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **Thoughtworks Mingle**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Thoughtworks Mingle**.

    ![Odkaz Thoughtworks Mingle v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-thoughtworks-mingle-test-user"></a>Vytvořit testovacího uživatele Thoughtworks Mingle

Aby se uživatelé Azure AD mohli přihlásit, musí se zřídit v aplikaci Thoughtworks Mingle s použitím jejich Azure Active Directory uživatelských jmen. V případě Thoughtworks Mingle je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu společnosti Thoughtworks Mingle jako správce.

2. Klikněte na **profil**.
   
    ![Váš první projekt](./media/thoughtworks-mingle-tutorial/ic785160.png "Váš první projekt")

3. Klikněte na kartu **správce** a potom klikněte na **Uživatelé**.
   
    ![Uživatelé](./media/thoughtworks-mingle-tutorial/ic785161.png "Uživatelé")

4. Klikněte na **Nový uživatel**.
   
    ![Nový uživatel](./media/thoughtworks-mingle-tutorial/ic785162.png "Nový uživatel")

5. Na stránce **Nový uživatel** proveďte následující kroky:
   
    ![Nový uživatel – dialogové okno](./media/thoughtworks-mingle-tutorial/ic785163.png "Nový uživatel")  
 
    a. Zadejte **přihlašovací jméno**, **zobrazované jméno**, **Vyberte heslo**, **potvrďte heslo** platného účtu Azure AD, který chcete zřídit v souvisejících textových polích. 

    b. Jako **typ uživatele** vyberte **úplný uživatel**.

    c. Klikněte na **vytvořit tento profil**.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů Thoughtworks Mingle nebo rozhraní API poskytovaná Thoughtworks Mingle.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Thoughtworks Mingle, měli byste být automaticky přihlášeni k Thoughtworks Mingle, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)