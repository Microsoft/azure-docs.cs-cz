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
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484842"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Kurz: Azure Active Directory integrací se službou Symantec Web Security Service (WSS)

V tomto kurzu se dozvíte, jak pomocí účtu Azure Active Directory (Azure AD) integrovat účet Symantec Web Security Service (WSS), aby služba WSS mohla ověřit koncového uživatele zřízené ve službě Azure AD pomocí ověřování SAML a vynutilit pravidla zásad uživatele nebo skupiny.

Integrace služby WSS Web Security Service (WSS) se službou Azure AD poskytuje následující výhody:

- Spravujte všechny koncové uživatele a skupiny používané vaším účtem WSS z portálu Azure AD.

- Umožněte koncovým uživatelům, aby se mohli sami ověřit ve WSS pomocí svých přihlašovacích údajů Azure AD.

- Povolte vynucování pravidel zásad uživatelů a úrovní skupin definovaných ve vašem účtu WSS.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (WSS) Symantec Web Security Service (WSS).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Služba WSS (Symantec Web Security Service) podporuje jednotné přihlašování (SSO) **IDP** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Přidání služby WSS (Symantec Web Security Service) z Galerie

Pokud chcete nakonfigurovat integraci služby WSS Web Security Service (WSS) od Symantec do Azure AD, musíte z Galerie přidat službu WSS Web Security Service (WSS) do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Symantec Web Security Service (WSS)** .
1. Z panelu výsledků vyberte **Symantec Web Security Service (WSS)** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro Symantec Web Security Service (WSS)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Symantec Web Security Service (WSS) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě WSS (Symantec Web Security Service).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby WSS (Symantec Web Security Service), proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování (WSS) Symantec Web Security Service (WSS)](#configure-symantec-web-security-service-wss-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele služby WSS (Web Security Service)](#create-symantec-web-security-service-wss-test-user)** , abyste měli protějšek B. Simon ve službě WSS (Symantec Web Security Service), která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Web Security Service (WSS) pro Symantec** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Obraťte se na [tým podpory pro klienta podpory Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) . hodnoty pro **identifikátor** a **adresu URL odpovědi** z nějakého důvodu nefungují. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup ke službě Symantec Web Security Service (WSS).

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Symantec Web Security Service (WSS)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-symantec-web-security-service-wss-sso"></a>Konfigurace jednotného přihlašování (WSS) Symantec Web Security Service (WSS)

Pokud chcete nakonfigurovat jednotné přihlašování na straně Symantec Web Security Service (WSS), přečtěte si dokumentaci k WSS online. Stažené **federační metadata XML** bude nutné importovat na portál WSS. Pokud potřebujete pomoc s konfigurací na portálu WSS, obraťte se na [tým podpory služby WSS Web Security (WSS) společnosti Symantec](https://www.symantec.com/contact-us) .

### <a name="create-symantec-web-security-service-wss-test-user"></a>Vytvořit testovacího uživatele pro službu Symantec Web Security Service (WSS)

V této části vytvoříte uživatele s názvem Britta Simon ve službě Symantec Web Security Service (WSS). Příslušné koncové uživatelské jméno se dá ručně vytvořit na portálu WSS nebo můžete počkat na to, že se uživatelé nebo skupiny zřízené ve službě Azure AD budou synchronizovat na portál WSS po pár minutách (asi 15 minut). Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. Veřejnou IP adresu počítače koncového uživatele, který se použije k procházení webů, je potřeba zřídit taky na portálu WSS (Symantec Web Security Service).

> [!NOTE]
> Pokud chcete získat veřejnou IP adresu vašeho počítače, klikněte prosím [sem](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) .

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni ke službě WSS Web Security Service (WSS), pro kterou jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici služba WSS (Web Security Service) společnosti Symantec v okně moje aplikace byste měli být automaticky přihlášeni ke službě WSS Web Security Service (WSS), pro kterou jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Jakmile nakonfigurujete službu WSS (Symantec Web Security Service), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
