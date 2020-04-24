---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím Trend Micro Web Security (TMWS) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trend Micro Web Security (TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083321"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s využitím Trend Micro Web Security (TMWS)

V tomto kurzu se dozvíte, jak integrovat Trend Micro Web Security (TMWS) s Azure Active Directory (Azure AD). Když integrujete Trend Micro Web Security (TMWS) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TMWS (Trend Micro Web Security).
* Umožněte, aby se vaši uživatelé automaticky přihlásili k programu Trend Micro Web Security (TMWS) s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné TMWS (Trend Micro Web Security) s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TMWS (Trend Micro Web Security) podporuje jednotné přihlašování (SSO) spouštěné v **SP**
* Jakmile nakonfigurujete Trend Micro Web Security (TMWS), můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Přidání TMWS (Trend Micro Web Security) z Galerie

Pokud chcete nakonfigurovat integraci TMWS (Trend Micro Web Security) do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Trend Micro Web Security (TMWS) z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **Trend Micro Web Security (TMWS)** .
1. Z panelu výsledků vyberte **Trend Micro Web Security (TMWS)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Trend Micro Web Security (TMWS)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Trend Micro Web Security (TMWS) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Trend Micro Web Security (TMWS).

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje Trend Micro Web Security (TMWS), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
    1. **[Konfigurace nastavení synchronizace uživatelů a skupin ve službě Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** – konfigurace nastavení synchronizace uživatelů a skupin ve službě Azure AD
1. **[Nakonfigurujte jednotné přihlašování TMWS (Trend Micro Web Security)](#configure-trend-micro-web-security-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **TMWS (Trend Micro Web Security)** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Hodnota identifikátoru není reálné číslo. Aktualizuje tuto hodnotu skutečným identifikátorem. Tyto hodnoty můžete získat v části **Nastavení poskytovatele služeb v oblasti portálu pro správu Azure** na obrazovce **metoda ověřování** pro azure AD ze **správy > adresářových služeb**.

1. Aplikace Trend Micro Web Security (TMWS) očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace Trend Micro Web Security (TMWS) několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Název | Zdrojový atribut|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | Názvu | User. userPrincipalName |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení TMWS (Trend Micro Web Security)** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k webu Trend Micro Web Security (TMWS).

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Trend Micro Web Security (TMWS)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurace nastavení synchronizace uživatelů a skupin ve službě Azure AD

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. V části **Spravovat**klikněte na **Registrace aplikací** a potom v oblasti **všechny aplikace** klikněte na novou podnikovou aplikaci.

1. V části **Spravovat**klikněte na **certifikáty & tajných**kódů.

1. V zobrazené oblasti klientských tajných klíčů klikněte na **nový tajný klíč klienta**.

1. Na obrazovce Přidat tajný klíč klienta, který se zobrazí, Volitelně přidejte popis a vyberte období vypršení platnosti tajného klíče klienta a pak klikněte na **Přidat**. Nově přidaný tajný klíč klienta se zobrazí v oblasti tajné klíče klienta.

1. Poznamenejte si hodnotu. Později budete zadávat informace do TMWS.

1. V části **Spravovat**klikněte na **oprávnění API**. 

1. Na obrazovce oprávnění rozhraní API, které se zobrazí, klikněte na **Přidat oprávnění**.

1. Na kartě rozhraní Microsoft API na obrazovce žádosti rozhraní API, které se zobrazí, klikněte na **Microsoft Graph** a pak na **oprávnění aplikace**.

1. Vyhledejte a přidejte následující oprávnění: 

    * Group.Read.All
    * User. Read. All

1. Klikněte na tlačítko **Přidat oprávnění**. Zobrazí se zpráva s potvrzením, že vaše nastavení bylo úspěšně uloženo. Nově přidaná oprávnění se zobrazí na obrazovce oprávnění rozhraní API.

1. V oblasti udělení souhlasu klikněte na **udělit souhlas správce < účtu správce > (výchozí adresář)** a pak na **Ano**. Zobrazí se zpráva s potvrzením, že byl úspěšně udělen souhlas správce pro požadovaná oprávnění.

1. Klikněte na **Přehled**. 

1. V pravém podokně, které se zobrazí, zaznamenejte ID aplikace (klienta) a ID adresáře (tenant). Později budete zadávat informace do TMWS. V Azure Active Directory můžete taky kliknout na **vlastní názvy domén** **> spravovat** a zaznamenávat název domény v pravém podokně.

## <a name="configure-trend-micro-web-security-sso"></a>Konfigurace služby Trend Micro Web Security SSO

1. Přihlaste se ke konzole pro správu TMWS a pokračujte na **správu** > **Uživatelé & ověřování** > **adresářové služby**.

1. V horní části obrazovky klikněte sem.

1. Na obrazovce metoda ověřování, která se zobrazí, klikněte na **Azure AD**.

1. Kliknutím **na zapnout** nebo **vypnout** se rozhodněte, jestli chcete, aby uživatelé služby AD ve vaší organizaci NAVŠTÍVILi weby prostřednictvím TMWS, pokud jejich data nejsou synchronizovaná s TMWS.

    > [!NOTE]
    > Uživatele, kteří nejsou synchronizovaný ze služby Azure AD, se dají ověřovat jenom prostřednictvím známých bran TMWS nebo vyhrazeného portu pro vaši organizaci.

1. V části **nastavení zprostředkovatele identity** proveďte následující kroky:

    a. Do pole **Adresa URL služby** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal

    b. Do pole **atribut přihlašovacího jména** vložte název deklarace identity uživatele pomocí zdrojového atributu **User. onpremisessamaccountname** z Azure Portal.

    c. V poli **veřejný certifikát SSL** použijte stažený **certifikát (Base64)** z Azure Portal.

1. V části **Nastavení synchronizace** proveďte následující kroky:

    a. V poli **tenant (tenant** ) použijte v Azure Portal hodnotu **ID adresáře (tenant)** nebo **vlastní název domény** .

    b. V poli **ID aplikace** , hodnota **ID aplikace (klienta)** z Azure Portal.

    c. V poli **tajného klíče klienta** použijte **tajný klíč klienta** z Azure Portal.

    d. V poli **plán synchronizace** vyberte možnost synchronizace se službou Azure AD ručně nebo podle plánu. Pokud zvolíte možnost ručně, nezapomeňte přejít zpět na obrazovku adresářové služby a provést ruční synchronizaci, aby informace v TMWS zůstaly aktuální.

    e. Klikněte na **Test připojení** a zkontrolujte, jestli se služba Azure AD může úspěšně připojit. 
    
    f. Klikněte na **Uložit**.
 
 > [!NOTE]
 > Další informace o tom, jak nakonfigurovat Trend Micro Web Security pomocí Azure AD, najdete v [tomto](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) dokumentu.

## <a name="test-sso"></a>Test SSO 

Po úspěšné konfiguraci služby Azure AD a zadání Azure AD jako metody ověřování uživatele se můžete přihlásit k proxy server TMWS a ověřit si instalaci. Když přihlášení Azure AD ověří váš účet, můžete navštívit Internet.

> [!NOTE]
> TMWS nepodporuje testování jednotného přihlašování z portálu Azure AD, v části Přehled > jednotné přihlašování > nastavení jednotného přihlašování pomocí > testu SAML vaší nové podnikové aplikace.

1. Zrušte zaškrtnutí políček prohlížeče všech souborů cookie a pak restartujte prohlížeč. 

1. Nasměrujte svůj prohlížeč na TMWS proxy server. Podrobnosti najdete v tématu [předávání provozu pomocí souborů PAC](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Navštivte libovolný internetový web. TMWS vás přesměruje na portál TMWS pro všechny uživatele.

1. Zadejte účet služby Active Directory (formát: domain\sAMAccountName nebo sAMAccountName@domain) nebo e-mailovou adresu nebo hlavní název uživatele (UPN) a klikněte na tlačítko **Přihlásit**se. TMWS vás pošle na přihlášení k Azure AD.

1. Do pole přihlášení k Azure AD zadejte přihlašovací údaje účtu služby Active Directory. Měli byste se úspěšně přihlásit k TMWS.

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Trend Micro Web Security (TMWS) s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit Trend Micro Web Security (TMWS) s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

