---
title: 'Kurz: Azure Active Directory integrace se skleníkem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a skleníkem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 0eb64ebe5e55bc054b6a280ac249cf451bb027db
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897357"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Kurz: Azure Active Directory integrace se skleníkem

V tomto kurzu se dozvíte, jak integrovat skleníky s Azure Active Directory (Azure AD). Když Integrujte skleníky s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke skleníkům
* Umožněte uživatelům, aby se do skleníku automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povolenou jednotným přihlašováním (SSO).

> [!NOTE] 
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu. 

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Skleník podporuje jednotné přihlašování s využitím služeb **SP**

## <a name="adding-greenhouse-from-the-gallery"></a>Přidání skleníků z Galerie

Pokud chcete nakonfigurovat integraci skleníků do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat skleník z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **skleníkový** text.
1. Na panelu výsledků vyberte **skleník** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Konfigurace a testování jednotného přihlašování Azure AD pro skleník

Nakonfigurujte a otestujte jednotné přihlašování Azure AD se skleníkovým uživatelem pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve sklenících.

K nakonfigurování a testování jednotného přihlašování služby Azure AD se sadou skleníků proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurovat jednotné přihlašování skleníku](#configure-greenhouse-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
    1. **[Vytvořte testovacího uživatele](#create-greenhouse-test-user)** s neBrittam testem, který je spojený s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **skleníku** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.greenhouse.io`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.greenhouse.io`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory společnosti skleník](https://www.greenhouse.io/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavit skleníky** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke skleníkům.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **skleník**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-greenhouse-sso"></a>Konfigurace jednotného přihlašování skleníku

1. V jiném okně webového prohlížeče se přihlaste na web skleníku jako správce.

1. V části **konfigurace > Dev Center > jednotné přihlašování**.

    ![snímek obrazovky se stránkou jednotného přihlašování](./media/greenhouse-tutorial/configure.png)

1. Na jedné Sign-On stránce proveďte následující kroky.

    ![snímek obrazovky se stránkou konfigurace jednotného přihlašování](./media/greenhouse-tutorial/sso-page.png)

    a. Kopírovat hodnotu **adresy URL spotřebitele kontrolního jednotného přihlašování** . tuto hodnotu vložte do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    b. Do textového pole **ID entity/vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **URL s jedním Sign-On** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený **soubor XML federačních metadat** z Azure Portal do programu Poznámkový blok a vložte obsah do textového pole **otisku certifikátu IDP** .

    e. V rozevíracím seznamu vyberte hodnotu **formátu identifikátoru názvu** .

    f. Klikněte na tlačítko **zahájit testování**.

    >[!NOTE]
    >Případně můžete také odeslat soubor **XML s federačními metadaty** kliknutím na možnost **zvolit soubor** .

### <a name="create-greenhouse-test-user"></a>Vytvořit uživatele skleníkového testu

Aby se uživatelé Azure AD mohli přihlásit ke skleníku, musí se zřídit do skleníku. V případě skleníku je zřizování ručním úkolem.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API skleníku, které poskytuje skleníková společnost. 

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se k webu **skleníkové** společnosti jako správce.

2. Přejít na adresu **> uživatelé > noví uživatelé**
   
    ![Uživatelé](./media/greenhouse-tutorial/create-user-1.png "Uživatelé")

4. V části **Přidat nové uživatele** proveďte následující kroky:
   
    ![Přidat nového uživatele](./media/greenhouse-tutorial/create-user-2.png "Přidat nového uživatele")

    a. Do textového pole zadejte e- **mailové zprávy uživatele** zadejte e-mailovou adresu platného Azure Active Directory účtu, který chcete zřídit.

    b. Klikněte na **Uložit**.    
   
      >[!NOTE]
      >Držitelům účtu Azure Active Directory obdržíte e-mail s odkazem na potvrzení účtu, než začne být aktivní.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení skleníku, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlašování pomocí skleníku přímo a spustit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici skleníků v okně moje aplikace, přesměruje se na přihlašování do nestandardu. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete sadu skleníků, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).