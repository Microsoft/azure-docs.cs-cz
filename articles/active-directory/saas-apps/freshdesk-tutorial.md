---
title: 'Kurz: Azure Active Directory integrace s Freshdesku | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshdesku.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: f120554574f8dc7d0b97744af6dad447b404005b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92451525"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Azure Active Directory integrace s Freshdesku

V tomto kurzu se dozvíte, jak integrovat Freshdesku s Azure Active Directory (Azure AD).
Integrace Freshdesku s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Freshdesku.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Freshdesku (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Freshdesku potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Freshdeskum jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshdesku podporuje jednotné přihlašování iniciované v **SP**
* Po nakonfigurování Freshdesku můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Přidání Freshdesku z Galerie

Pokud chcete nakonfigurovat integraci Freshdesku do služby Azure AD, musíte přidat Freshdesku z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **freshdesku** .
1. Na panelu výsledků vyberte **freshdesku** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Freshdesku

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Freshdesku pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freshdesku.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Freshdesku, dokončete následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování freshdesku](#configure-freshdesk-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvoření freshdesku Test User](#create-freshdesk-test-user)** – pro Britta Simon v freshdesku, který je propojený s reprezentací uživatele Azure AD.
1. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Freshdesku** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.freshdesk.com` nebo jakékoliv jiné hodnoty, které freshdesku navrhl.

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.freshdesk.com` nebo jakékoli jiné hodnoty, které freshdesku navrhl.
     
    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta freshdesku](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Freshdesku aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, zatímco **jedinečný identifikátor uživatele** je namapován pomocí **User. userPrincipalName** , ale freshdesku očekává, že tato deklarace identity bude namapována s **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu Upravit a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení freshdesku** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Freshdesku.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **freshdesku**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **freshdesku**.

    ![Odkaz Freshdesku v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-freshdesk-single-sign-on"></a>Nakonfigurovat Freshdesku jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Freshdesku společnosti jako správce.

2. Vyberte **ikonu zabezpečení** a v části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/configure-1.png "Jednotné přihlašování")
  
    a. V případě **jednotného přihlašování**vyberte **zapnuto**.

    b. V **metodě přihlášení**vyberte **jednotné přihlašování SAML**.

    c. Do **ID entity poskytnuté** textovým polem IDP vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **URL jednotného přihlašování SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. V **možnostech podepisování**vyberte v rozevíracím seznamu **pouze podepsané kontrolní výrazy** .

    f. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    například Do textového pole **certifikát zabezpečení** vložte hodnotu **certifikátu (Base64)** , kterou jste získali dříve.
  
    h. Klikněte na **Uložit**.

## <a name="create-freshdesk-test-user"></a>Vytvořit testovacího uživatele Freshdesku

Aby se uživatelé Azure AD mohli přihlašovat k Freshdesku, musí se zřídit v Freshdesku.  
V případě Freshdesku je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **freshdesku** .

1. V nabídce na levé straně klikněte na **správce** a na kartě **Obecné nastavení** klikněte na **agenti**.
  
    ![Agenti](./media/freshdesk-tutorial/create-user-1.png "Agenti")

1. Klikněte na **nový agent**.

    ![Nový agent](./media/freshdesk-tutorial/create-user-2.png "Nový agent")

1. V dialogovém okně informace o agentovi zadejte požadovaná pole a klikněte na **vytvořit agenta**.

    ![Informace o agentovi](./media/freshdesk-tutorial/create-user-3.png "Informace o agentovi")

    >[!NOTE]
    >Držitel účtu Azure AD obdrží e-mail s odkazem na potvrzení účtu před jeho aktivací.
    >
    >[!NOTE]
    >K zřizování uživatelských účtů Azure AD pro Freshdesku můžete použít jiné nástroje pro vytváření uživatelských účtů Freshdesku nebo rozhraní API poskytovaná Freshdesku.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Freshdesku na přístupovém panelu, měli byste se automaticky přihlásit k Freshdesku, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)