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
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504045"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s novým Relic

V tomto kurzu se dozvíte, jak integrovat nové Relic s Azure Active Directory (Azure AD). Když integrujete nové Relic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k novým Relic.
* Umožněte uživatelům, aby se automaticky přihlásili k novým Relicům pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Nové předplatné Relic s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* New Relic podporuje jednotné přihlašování iniciované poskytovatelem služeb nebo poskytovatelem identity.
* Po nakonfigurování nové Relic můžete vynutili řízení relace, které chrání před exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Přidání nových Relic z Galerie

Pokud chcete nakonfigurovat integraci nových Relic do služby Azure AD, musíte z Galerie přidat **nové Relic (podle organizace)** do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. Vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**  >  **Nová aplikace**.
1. Na stránce **Procházet galerii Azure AD** zadejte do vyhledávacího pole **Nový Relic (podle organizace)** .
1. Z výsledků vyberte **Nový Relic (podle organizace)** a pak vyberte **vytvořit**. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurace a testování jednotného přihlašování Azure AD pro nové Relic

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí New Relic pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v New Relic.

Konfigurace a testování jednotného přihlašování Azure AD pomocí nových Relic:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
   1. [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí B. Simon.
   1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, [přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) .
1. [Nakonfigurujte nové](#configure-new-relic-sso) jednotné přihlašování Relic pro konfiguraci nastavení jednotného přihlašování na nové straně Relic.
   1. [Vytvořte nového uživatele Relic test](#create-a-new-relic-test-user) , který bude mít protiprotějšek pro B. Simon v novém Relic propojených s uživatelem služby Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce Nová integrace aplikace **Relic podle organizace** najděte část **Správa** . Pak vyberte **jednotné přihlašování**.

1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky s nastavením jednoduchého Sign-On s SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

1. V části **základní konfigurace SAML** vyplňte hodnoty pro **identifikátor** a **adresu URL odpovědi**.

   * Načtěte tyto hodnoty pomocí nové Relic **Moje organizační** aplikace. Použití této aplikace:
      1. [Přihlaste](https://login.newrelic.com/) se k nové Relic.
      1. V horní nabídce vyberte **aplikace**.
      1. V části **vaše aplikace** vyberte **Moje**  >  **domény ověřování** organizace.
      1. Vyberte doménu ověřování, ke které chcete připojit službu Azure AD SSO (Pokud máte více než jednu doménu ověřování). Většina společností má pouze jednu doménu ověřování nazvanou **výchozí**. Pokud je k dispozici pouze jedna doména ověřování, nemusíte nic vybírat.
      1. V části **ověřování** obsahuje **Adresa URL příjemce kontrolního výrazu** hodnotu, která se má použít pro **adresu URL odpovědi**.
      1. V části **ověřování** obsahuje **naše ID entity** hodnotu, která se má použít pro **identifikátor**.

1. V části **atributy uživatele & deklarace identity** se ujistěte, že je **jedinečný identifikátor uživatele** namapován na pole, které obsahuje e-mailovou adresu použitou v New Relic.

   * Výchozí pole **User. userPrincipalName** bude fungovat, pokud jsou hodnoty stejné jako nové e-mailové adresy Relic.
   * V případě, že uživatel **. userPrincipalName** není novou e-mailovou adresou Relic, může být pro vás vhodnější pole **uživatel. mail** .

1. V části **podpisový certifikát SAML** zkopírujte **adresu URL federačních metadat aplikace** a uložte její hodnotu pro pozdější použití.

1. V části **nastavit nové Relic podle organizace** zkopírujte **přihlašovací adresu URL** a uložte její hodnotu pro pozdější použití.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Zde je postup, jak vytvořit testovacího uživatele ve Azure Portal s názvem B. Simon.

1. Z Azure Portal vyberte možnost **Azure Active Directory**.
1. Vyberte **Uživatelé**  >  **Nový uživatel**.
1. Na stránce **Nový uživatel** :
   1. Do pole **uživatelské jméno** zadejte `username@companydomain.extension` . Například, `b.simon@contoso.com`. Tato adresa by měla odpovídat e-mailové adrese, kterou budete používat na nové straně Relic.
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Vyberte možnost **Zobrazit heslo** a pak zobrazovanou hodnotu uložte.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

Tady je postup, jak povolit B. Simon pro použití jednotného přihlašování Azure AD tím, že udělíte přístup k novému Relic aplikace organizace.

1. Z Azure Portal vyberte možnost **Azure Active Directory**.
1. Vyberte **podnikové aplikace**  >  **nové Relic podle organizace**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Snímek obrazovky s oddílem spravovat se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V nabídce **Přidat přiřazení** vyberte **uživatele a skupiny** (nebo **uživatele**) v závislosti na vaší úrovni plánu.

   ![Snímek obrazovky možnosti Přidat uživatele](common/add-assign-user.png)

1. V části **Uživatelé a skupiny** ( **nebo uživatelé**) v seznamu **Uživatelé** vyberte **B. Simon** a pak zvolte **Vybrat** v dolní části obrazovky.
1. V **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-new-relic-sso"></a>Konfigurace nového jednotného přihlašování Relic

Pomocí těchto kroků můžete nakonfigurovat jednotné přihlašování v New Relic.

1. [Přihlaste](https://login.newrelic.com/) se k nové Relic.

1. V horní nabídce vyberte **aplikace**.

1. V části **vaše aplikace** vyberte **Moje**  >  **domény ověřování** organizace.

1. Vyberte doménu ověřování, ke které chcete připojit službu Azure AD SSO (Pokud máte více než jednu doménu ověřování). Většina společností má pouze jednu doménu ověřování nazvanou **výchozí**. Pokud je k dispozici pouze jedna doména ověřování, nemusíte nic vybírat.

1. V části **ověřování** vyberte **Konfigurovat**.

   1. Pro **zdroj METADAT SAML** zadejte hodnotu, kterou jste předtím uložili z pole **Adresa URL federačních METADAT aplikace** Azure AD.

   1. V poli adresa **URL cíle jednotného přihlašování** zadejte hodnotu, kterou jste předtím uložili z pole **přihlašovací adresa URL** služby Azure AD.

   1. Po ověření, že nastavení vypadá dobře na stranách Azure AD i na nových Relic, vyberte **Uložit**. Pokud nejsou obě strany správně nakonfigurované, uživatelé se nebudou moct přihlašovat k novým Relic.

### <a name="create-a-new-relic-test-user"></a>Vytvořit nového Relic testovacího uživatele

V této části vytvoříte uživatele s názvem B. Simon v New Relic.

1. [Přihlaste](https://login.newrelic.com/) se k nové Relic.

1. V horní nabídce vyberte **aplikace**.

1. V části **vaše aplikace** vyberte **Správa uživatelů**.

1. Vyberte možnost **Přidat uživatele**.

   1. Jako **název** zadejte **B. Simon**.
   
   1. Jako **e-mail** zadejte hodnotu, která se pošle pomocí jednotného přihlašování Azure AD.
   
   1. Vyberte **typ** uživatele a **skupinu** uživatelů pro uživatele. Pro testovacího uživatele mají přijatelné možnosti **základní uživatel** pro typ a **uživatele** pro skupinu.
   
   1. Uživatele uložíte tak, že vyberete **Přidat uživatele**.

## <a name="test-sso"></a>Test SSO 

Tady je postup testování konfigurace jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete **Nový Relic podle organizace** , měli byste být automaticky přihlášeni k nové Relic. Další informace o přístupovém panelu najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si nové Relic s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
