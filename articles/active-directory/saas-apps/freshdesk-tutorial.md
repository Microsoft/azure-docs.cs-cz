---
title: 'Kurz: Azure Active Directory integrace s Freshdesku | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshdesku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74227618"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Azure Active Directory integrace s Freshdesku

V tomto kurzu se dozvíte, jak integrovat Freshdesku s Azure Active Directory (Azure AD).
Integrace Freshdesku s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Freshdesku.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Freshdesku (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Freshdesku potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Freshdeskum jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshdesku podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání Freshdesku z Galerie

Pokud chcete nakonfigurovat integraci Freshdesku do služby Azure AD, musíte přidat Freshdesku z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Freshdesku z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **freshdesku**, vyberte **freshdesku** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Freshdesku v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Freshdesku na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freshdesku.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Freshdesku, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování freshdesku](#configure-freshdesk-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření freshdesku Test User](#create-freshdesk-test-user)** – pro Britta Simon v freshdesku, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Freshdesku, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **freshdesku** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Freshdesku a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.freshdesk.com` nebo jakékoliv jiné hodnoty, které freshdesku navrhl.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.freshdesk.com` nebo jakékoli jiné hodnoty, které freshdesku navrhl.

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta freshdesku](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Freshdesku aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Příklad ukazuje následující snímek obrazovky. Výchozí hodnota **jedinečného identifikátoru uživatele** je **User. userPrincipalName,** ale freshdesku očekává, že bude namapován pomocí e-mailové adresy uživatele. Pro tuto funkci můžete použít atribut **User. mail** ze seznamu nebo použít odpovídající hodnotu atributu na základě konfigurace vaší organizace. 

    ![image](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:
    
    | Název | Zdrojový atribut |
    | ---------------| --------------- |
    | Jedinečný identifikátor uživatele | uživatel. pošta |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    g. Klikněte na **Uložit**.

7. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

8. Otevřete **příkazový řádek** a spusťte následující příkazy:

    a. Do `certutil.exe -dump FreshDesk.cer` příkazového řádku zadejte hodnotu.

    > [!NOTE]
    > Zde **freshdesku. cer** certifikát, který jste stáhli z Azure Portal.

    b. Zkopírujte hodnotu **hash certifikátu (SHA256)** a vložte ji do poznámkového bloku. 

9. V části **Nastavení freshdesku** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-freshdesk-single-sign-on"></a>Konfigurace jednotného přihlašování Freshdesku

1. V jiném okně webového prohlížeče se přihlaste k webu Freshdesku společnosti jako správce.

2. Vyberte **ikonu nastavení** a v části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/IC776770.png "Jednotné přihlašování")
  
    a. V případě **jednotného přihlašování (SSO)** vyberte **zapnuto**.

    b. Vyberte **jednotné přihlašování SAML**.

    c. Do textového pole **Adresa URL přihlášení SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    e. V textovém poli **otisk certifikátu zabezpečení** vložte hodnotu **hash certifikátu (SHA256)** , kterou jste získali dříve.
  
    f. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Freshdesku.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **freshdesku**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **freshdesku**.

    ![Odkaz Freshdesku v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-freshdesk-test-user"></a>Vytvořit testovacího uživatele Freshdesku

Aby se uživatelé Azure AD mohli přihlašovat k Freshdesku, musí se zřídit v Freshdesku.  
V případě Freshdesku je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **freshdesku** .

2. V nabídce v horní části klikněte na **správce**.

    ![Správce](./media/freshdesk-tutorial/IC776772.png "Správce")

3. Na kartě **Obecné nastavení** klikněte na **agenti**.
  
    ![Agenti](./media/freshdesk-tutorial/IC776773.png "Agenti")

4. Klikněte na **nový agent**.

    ![Nový agent](./media/freshdesk-tutorial/IC776774.png "Nový agent")

5. V dialogovém okně informace o agentovi proveďte následující kroky:

    ![Informace o agentovi](./media/freshdesk-tutorial/IC776775.png "Informace o agentovi")

    a. Do textového pole **e-mail** zadejte e-mailovou adresu Azure AD účtu Azure AD, který chcete zřídit.

    b. Do textového pole **celé jméno** zadejte název účtu Azure AD, který chcete zřídit.

    c. Do textového pole **název** zadejte název účtu Azure AD, který chcete zřídit.

    d. Klikněte na **Uložit**.

    >[!NOTE]
    >Držitel účtu Azure AD obdrží e-mail s odkazem na potvrzení účtu před jeho aktivací.
    >
    >[!NOTE]
    >K zřizování uživatelských účtů Azure AD pro Freshdesku můžete použít jiné nástroje pro vytváření uživatelských účtů Freshdesku nebo rozhraní API poskytovaná Freshdesku.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Freshdesku, měli byste se automaticky přihlásit k Freshdesku, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

