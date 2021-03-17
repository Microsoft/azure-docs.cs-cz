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
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651900"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Kurz: Azure Active Directory integrace s Freshdesku

V tomto kurzu se dozvíte, jak integrovat Freshdesku s Azure Active Directory (Azure AD). Když integrujete Freshdesku s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Freshdesku.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Freshdesku svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Freshdeskum jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Freshdesku podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-freshdesk-from-the-gallery"></a>Přidání Freshdesku z Galerie

Pokud chcete nakonfigurovat integraci Freshdesku do služby Azure AD, musíte přidat Freshdesku z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **freshdesku** .
1. Na panelu výsledků vyberte **freshdesku** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Freshdesku

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Freshdesku pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Freshdesku.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Freshdesku postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte FRESHDESKU SSO](#configure-freshdesk-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvoření freshdesku Test User](#create-freshdesk-test-user)** – pro Britta Simon v freshdesku, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

1. V Azure Portal na stránce integrace aplikací **freshdesku** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

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


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Freshdesku.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **freshdesku**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-freshdesk-sso"></a>Konfigurace jednotného přihlašování Freshdesku

1. V jiném okně webového prohlížeče se přihlaste k webu Freshdesku společnosti jako správce.

2. Vyberte **ikonu zabezpečení** a v části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshdesk-tutorial/configure-1.png "Jednotné přihlašování")
  
    a. V případě **jednotného přihlašování** vyberte **zapnuto**.

    b. V **metodě přihlášení** vyberte **jednotné přihlašování SAML**.

    c. Do **ID entity poskytnuté** textovým polem IDP vložte hodnotu **ID entity** , kterou jste zkopírovali z Azure Portal.

    d. V textovém poli **URL jednotného přihlašování SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    e. V **možnostech podepisování** vyberte v rozevíracím seznamu **pouze podepsané kontrolní výrazy** .

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

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Freshdesku, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Freshdesku přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Freshdesku v okně moje aplikace, měli byste se automaticky přihlásit k Freshdesku, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Freshdesku můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).