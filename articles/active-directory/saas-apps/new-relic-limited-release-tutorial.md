---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s novým Relic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a novými Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327062"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s novým Relic

V tomto kurzu se dozvíte, jak integrovat nové Relic s Azure Active Directory (Azure AD). Když integrujete nové Relic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k novým Relic.
* Umožněte uživatelům, aby se automaticky přihlásili k novým Relicům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Nové předplatné s povoleným jednotným přihlašováním (SSO) Relic.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Novinka Relic podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Po nakonfigurování nových Relic můžete vynutili řízení relace, které chrání před exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Přidání nové aplikace v Relic z Galerie

Pokud chcete nakonfigurovat integraci nových Relic do služby Azure AD, musíte z Galerie přidat **nové Relic (podle organizace)** do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. Vyberte službu **Azure Active Directory** .
1. Vyberte **podnikové aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. Na stránce **Procházet galerii Azure AD** zadejte do vyhledávacího pole **Nový Relic (podle organizace)** .
1. Z panelu výsledků vyberte **Nový Relic (podle organizace)** a pak vyberte **vytvořit**. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurace a testování jednotného přihlašování Azure AD pro nové Relic

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s novým Relic pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v New Relic.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí New Relic, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
   1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
   1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte nové jednotné přihlašování Relic](#configure-new-relic-sso)** – ke konfiguraci nastavení jednotného přihlašování na nové straně Relic.
   1. **[Vytvořte nového Relicho testovacího uživatele](#create-a-new-relic-test-user)** – pro B. Simon v novém Relic, který je propojený s uživatelem služby Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce Nová integrace aplikace **Relic podle organizace** najděte část **Správa** a vyberte **jednotné přihlašování**.

1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** vyplňte hodnoty pro **identifikátor** a **adresu URL odpovědi**.

   * Tyto hodnoty se načítají pomocí nové aplikace Relic **Moje organizace** . Chcete-li použít tuto aplikaci, proveďte tyto kroky:
      1. [Přihlaste](https://login.newrelic.com/) se k New Relic.
      1. V horní nabídce vyberte **aplikace**.
      1. V části **vaše aplikace** vyberte **Moje organizace**.
      1. Klikněte na **domény ověřování**.
      1. Vyberte doménu ověřování, ke které chcete připojit službu Azure AD SSO (Pokud máte více než jednu doménu ověřování). Většina společností má pouze jednu doménu ověřování nazvanou **výchozí**. Pouze v jedné doméně ověřování není nutné nic vybírat.
      1. V části **ověřování** obsahuje **Adresa URL příjemce kontrolního výrazu** hodnotu, která se má použít pro **adresu URL odpovědi**.
      1. V části **ověřování** obsahuje **naše ID entity** hodnotu, která se má použít pro **identifikátor**.

1. V části **atributy uživatele & deklarace identity** zajistěte, aby byl **jedinečný identifikátor uživatele** namapován na pole obsahující e-mailovou adresu, která se používá v New Relic.

   * Výchozí pole **User. userPrincipalName** bude fungovat, pokud se hodnoty shodují s novými e-mailovými adresami Relic.
   * V případě, že uživatel **. userPrincipalName** není novou e-mailovou adresou Relic, může být pro vás vhodnější použít pole **uživatel. mail** .

1. V části **podpisový certifikát SAML** zkopírujte **adresu URL federačních metadat aplikace** a uložte její hodnotu pro pozdější použití.

1. V části **nastavit nové Relic podle organizace** zkopírujte **přihlašovací adresu URL** a uložte její hodnotu pro pozdější použití.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. Z Azure Portal vyberte **Azure Active Directory** služba.
1. Vyberte **Uživatelé**.
1. Chcete-li přidat nového uživatele, vyberte v horní části obrazovky možnost **Nový uživatel** .
1. Na stránce **Nový uživatel** proveďte tyto kroky:
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `b.simon@contoso.com`. Tato adresa by měla odpovídat e-mailové adrese, kterou budete používat na nové straně Relic.
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Zaškrtněte políčko **Zobrazit heslo** a potom hodnotu zobrazenou v poli **počáteční heslo** uložte.
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure AD udělením přístupu k **nové Relic aplikace organizace** .

1. Z Azure Portal vyberte **Azure Active Directory** služba.
1. Vyberte **podnikové aplikace**.
1. V seznamu aplikace vyberte **Nový Relic podle organizace**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **uživatele a skupiny** (nebo **uživatele** v závislosti na vaší úrovni plánu).

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** (nebo **Uživatelé**) v seznamu Uživatelé vyberte **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-new-relic-sso"></a>Konfigurace nového jednotného přihlašování Relic

Pomocí těchto kroků můžete nakonfigurovat jednotné přihlašování v New Relic.

1. [Přihlaste](https://login.newrelic.com/) se k New Relic.

1. V horní nabídce vyberte **aplikace**.

1. V části **vaše aplikace** vyberte **Moje organizace**.

1. Klikněte na **domény ověřování**.

1. Vyberte doménu ověřování, ke které chcete připojit službu Azure AD SSO (Pokud máte více než jednu doménu ověřování). Většina společností má pouze jednu doménu ověřování nazvanou **výchozí**. Pouze v jedné doméně ověřování není nutné nic vybírat.

1. V části **ověřování** klikněte na **Konfigurovat**.

   1. Do pole **zdroj METADAT SAML** zadejte hodnotu, kterou jste předtím uložili z pole **Adresa URL federačních METADAT aplikace** Azure AD.

   1. Do pole **Adresa URL cíle jednotného přihlašování** zadejte hodnotu, kterou jste předtím uložili z pole **Adresa URL pro přihlášení** na straně služby Azure AD.

   1. Po ověření nastavení na stránce Azure AD i na nových stranách Relic klikněte na **Uložit** . Pokud nejsou obě strany správně nakonfigurované, uživatelé se nebudou moct přihlašovat do nových Relic.

### <a name="create-a-new-relic-test-user"></a>Vytvořit nového Relic testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v New Relic. Chcete-li vytvořit uživatele, postupujte podle těchto kroků.

1. [Přihlaste](https://login.newrelic.com/) se k New Relic.

1. V horní nabídce vyberte **aplikace**.

1. V části **vaše aplikace** vyberte **Správa uživatelů**.

1. Klikněte na tlačítko **Přidat uživatele** .

   1. Do pole **název** zadejte **B. Simon**.
   
   1. Do pole **e-mail** zadejte hodnotu, která se pošle pomocí jednotného přihlašování služby Azure AD.
   
   1. Vyberte **typ** uživatele a **skupinu** uživatelů pro uživatele. Pro testovacího uživatele mají přijatelné možnosti **základní uživatel** pro typ a **uživatele** pro skupinu.
   
   1. Kliknutím na **Přidat uživatele** uživatele uložte.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici **Nový Relic podle organizace** na přístupovém panelu byste měli být automaticky přihlášeni k nové Relic. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si nové Relic s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
