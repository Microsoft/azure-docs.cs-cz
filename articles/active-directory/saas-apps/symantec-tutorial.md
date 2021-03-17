---
title: 'Kurz: Azure Active Directory integrací se službou Symantec Web Security Service (WSS) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a službou Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518360"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Azure Active Directory integrací se službou Symantec Web Security Service (WSS)

V tomto kurzu se dozvíte, jak pomocí účtu Azure Active Directory (Azure AD) integrovat účet Symantec Web Security Service (WSS), aby služba WSS mohla ověřit koncového uživatele zřízené ve službě Azure AD pomocí ověřování SAML a vynutilit pravidla zásad uživatele nebo skupiny.

Integrace služby WSS Web Security Service (WSS) se službou Azure AD poskytuje následující výhody:

- Spravujte všechny koncové uživatele a skupiny používané vaším účtem WSS z portálu Azure AD.

- Umožněte koncovým uživatelům, aby se mohli sami ověřit ve WSS pomocí svých přihlašovacích údajů Azure AD.

- Povolte vynucování pravidel zásad uživatelů a úrovní skupin definovaných ve vašem účtu WSS.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se službou Symantec Web Security Service (WSS) potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním pro Symantec Web Security Service (WSS)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Služba WSS (Symantec Web Security Service) podporuje jednotné přihlašování (SSO) **IDP** .

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Přidání služby WSS (Symantec Web Security Service) z Galerie

Pokud chcete nakonfigurovat integraci služby WSS Web Security Service (WSS) od Symantec do Azure AD, musíte z Galerie přidat službu WSS Web Security Service (WSS) do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat službu WSS Web Security Service (WSS) z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Symantec Web Security Service (WSS)**, vyberte **Symantec Web Security Service (WSS)** z panelu výsledek a pak klikněte na **Přidat** tlačítko a přidejte aplikaci.

     ![Symantec Web Security Service (WSS) v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí služby WSS (Symantec Web Security Service) na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě WSS (Web Security Service) společnosti Symantec.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby WSS (Symantec Web Security Service), musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **Nakonfigurujte jednotné přihlašování (WSS) Symantec Web Security Service (WSS)** a nakonfigurujte nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele služby WSS (Web Security Service)](#create-symantec-web-security-service-wss-test-user)** , abyste měli protějšek Britta Simon ve službě WSS (Symantec Web Security Service), který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí služby WSS (Symantec Web Security Service), proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **služby webu Symantec Web Security (WSS)** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně a adresách URL společnosti Symantec Web Security Service (WSS)](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Obraťte se na [tým podpory pro klienta podpory Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) . hodnoty pro **identifikátor** a **adresu URL odpovědi** z nějakého důvodu nefungují. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Konfigurace jednoho Sign-On služby Symantec Web Security Service (WSS)

Pokud chcete nakonfigurovat jednotné přihlašování na straně Symantec Web Security Service (WSS), přečtěte si dokumentaci k WSS online. Stažené **federační metadata XML** bude nutné importovat na portál WSS. Pokud potřebujete pomoc s konfigurací na portálu WSS, obraťte se na [tým podpory služby WSS Web Security (WSS) společnosti Symantec](https://www.symantec.com/contact-us) .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup ke službě Symantec Web Security Service (WSS).

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **Symantec Web Security Service (WSS)**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Symantec Web Security Service (WSS)**.

    ![Odkaz na službu webu Symantec Web Security (WSS) v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-symantec-web-security-service-wss-test-user"></a>Vytvořit testovacího uživatele pro službu Symantec Web Security Service (WSS)

V této části vytvoříte uživatele s názvem Britta Simon ve službě Symantec Web Security Service (WSS). Příslušné koncové uživatelské jméno se dá ručně vytvořit na portálu WSS nebo můžete počkat na to, že se uživatelé nebo skupiny zřízené ve službě Azure AD budou synchronizovat na portál WSS po pár minutách (asi 15 minut). Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. Veřejnou IP adresu počítače koncového uživatele, který se použije k procházení webů, je potřeba zřídit taky na portálu WSS (Symantec Web Security Service).

> [!NOTE]
> Pokud chcete získat veřejnou IP adresu vašeho počítače, klikněte prosím [sem](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete funkci jednotného přihlašování teď, když jste nakonfigurovali účet WSS pro použití Azure AD pro ověřování SAML.

Po nakonfigurování webového prohlížeče na provoz proxy na WSS, když otevřete webový prohlížeč a zkusíte přejít na web, budete přesměrováni na přihlašovací stránku Azure. Zadejte přihlašovací údaje koncového uživatele testu, který byl zřízen ve službě Azure AD (tj. BrittaSimon) a přidruženém heslu. Po ověření budete moct přejít na web, který jste zvolili. Měli byste na straně WSS vytvořit pravidlo zásad, které zablokuje BrittaSimon k procházení na konkrétní lokalitu. při pokusu o přechod na tuto lokalitu jako uživatel BrittaSimon by se měla zobrazit stránka blok WSS.

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)