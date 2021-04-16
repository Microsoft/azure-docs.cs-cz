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
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 8c0ffe4affb6b30f2e2a1aa97a0f4795c130f59b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517602"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s novým Relic

V tomto kurzu se dozvíte, jak integrovat nové Relic s Azure Active Directory (Azure AD). Když integrujete nové Relic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k novým Relic.
* Umožněte uživatelům, aby se automaticky přihlásili k novým Relicům pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Nové předplatné Relic s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* New Relic podporuje jednotné přihlašování iniciované poskytovatelem služeb nebo poskytovatelem identity.

## <a name="add-new-relic-from-the-gallery"></a>Přidání nových Relic z Galerie

Pokud chcete nakonfigurovat integraci nových Relic do služby Azure AD, musíte z Galerie přidat **nové Relic (podle organizace)** do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
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

1. V Azure Portal na stránce Nová integrace aplikace **Relic podle organizace** najděte část **Správa** . Pak vyberte **jednotné přihlašování**.

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

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k novým Relic.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Nový Relic**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

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

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na novou adresu URL pro přihlášení k Relic, kde můžete spustit tok přihlášení.  

* Přejít na novou adresu URL pro přihlášení k Relic přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k novému Relic, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na novou dlaždici Relic v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k novému Relic, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování nových Relic můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
