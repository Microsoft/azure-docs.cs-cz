---
title: Průvodce správcem atlassian Jira/Confluence – Azure Active Directory| Dokumenty společnosti Microsoft
description: Průvodce pro správu, který používá Atlassian Jira a Confluence s Azure Active Directory (Azure AD)..
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8679f9a03fded546db68f058bca716ba053aa0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161206"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Průvodce správcem atlassian jira a confluence pro Azure Active Directory

## <a name="overview"></a>Přehled

Modul plug-in jednotného přihlašování (SSO) služby Azure Active Directory (Azure AD) umožňuje zákazníkům Microsoft Azure AD používat jejich pracovní nebo školní účet pro přihlášení k produktům založeným na atlassian jira a confluence server. Implementuje saml 2.0 založené přisuzu je.

## <a name="how-it-works"></a>Jak to funguje

Když se uživatelé chtějí přihlásit k aplikaci Atlassian Jira nebo Confluence, zobrazí se jim tlačítko **Přihlásit se pomocí Služby Azure AD** na přihlašovací stránce. Když ji vyberou, musí se přihlásit pomocí přihlašovací stránky organizace Azure AD (to znamená jejich pracovní nebo školní účet).

Po ověření uživatelů by měli být schopni přihlásit se k aplikaci. Pokud jsou již ověřeny pomocí ID a hesla pro svůj pracovní nebo školní účet, přihlásí se přímo k aplikaci. 

Přihlašovací práce přes Jira a Confluence. Pokud jsou uživatelé přihlášeni k aplikaci Jira a Confluence se otevře ve stejném okně prohlížeče, nemusí zadejte přihlašovací údaje pro druhou aplikaci. 

Uživatelé se také mohou k produktu Atlassian dostat prostřednictvím služby Moje aplikace pod pracovním nebo školním účtem. Měly by být přihlášeny, aniž by byly požádány o pověření.

> [!NOTE]
> Zřizování uživatelů se neprovádí prostřednictvím modulu plug-in.

## <a name="audience"></a>Cílová skupina

Jira a Confluence správci můžete použít modul plug-in k povolení přihlašování pomocí Azure AD.

## <a name="assumptions"></a>Předpoklady

* Instance Jira a Confluence jsou povoleny protokolem HTTPS.
* Uživatelé jsou již vytvořeni v Jira nebo Confluence.
* Uživatelé mají role přiřazené v Jira nebo Confluence.
* Správci mají přístup k informacím potřebným ke konfiguraci modulu plug-in.
* Jira nebo Confluence je k dispozici i mimo firemní síť.
* Zásuvný modul pracuje pouze s místní verzí Jira a Confluence.

## <a name="prerequisites"></a>Požadavky

Před instalací modulu plug-in si všimněte následujících informací:

* Jira a Confluence jsou nainstalovány v 64bitové verzi systému Windows.
* Verze Jira a Confluence jsou povoleny protokolem HTTPS.
* Jira a Confluence jsou k dispozici na internetu.
* Admin pověření jsou na místě pro Jira a Confluence.
* Přihlašovací údaje správce jsou na místě pro Azure AD.
* WebSudo je zakázán v Jira a Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Podporované verze Jira a Confluence

Plug-in podporuje následující verze Jira a Confluence:

* Jira Core a software: 6,0 až 7,12
* Jira Service Desk: 3.0.0 až 3.5.0
* JIRA také podporuje 5.2. Další podrobnosti zobrazíte kliknutím [na jednotné přihlašování služby Microsoft Azure Active Directory pro JIRA 5.2.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Soutok: 5,0 až 5,10
* Soutok: 6.0.1
* Soutok: 6.1.1
* Soutok: 6.2.1
* Soutok: 6.3.4
* Soutok: 6.4.0
* Soutok: 6.5.0
* Soutok: 6.6.2
* Soutok: 6.7.0
* Soutok: 6.8.1
* Soutok: 6.9.0
* Soutok: 6.10.0
* Soutok: 6.11.0
* Soutok: 6.12.0

## <a name="installation"></a>Instalace

Chcete-li modul plug-in nainstalovat, postupujte takto:

1. Přihlaste se k instanci Jira nebo Confluence jako správce.

2. Přejděte na konzolu pro správu Jira/Confluence a vyberte **doplňky**.

3. Ze služby Stažení softwaru společnosti Microsoft stáhněte [plugin Microsoft SAML SSO pro modul Microsoft](https://www.microsoft.com/download/details.aspx?id=56506)/ [SAML SSO pro confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Ve výsledcích hledání se zobrazí příslušná verze modulu plug-in.

4. Vyberte modul plug-in a nainstaluje jej univerzální správce modulů plug-in (UPM).

Po instalaci modulu plug-in se zobrazí v části **Nainstalované doplňky uživatele** **v části Spravovat doplňky**.

## <a name="plug-in-configuration"></a>Konfigurace modulu plug-in

Než začnete modul plug-in používat, je nutné jej nakonfigurovat. Vyberte modul plug-in, vyberte tlačítko **Konfigurovat** a zadejte podrobnosti o konfiguraci.

Následující obrázek znázorňuje konfigurační obrazovku v Jiře i Soutoku:

![Obrazovka konfigurace modulu plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

* **Adresa URL metadat:** Adresa URL pro získání metadat federace z Azure AD.

* **Identifikátory**: Adresa URL, kterou služba Azure AD používá k ověření zdroje požadavku. Mapuje se na prvek **Identifier** ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*\<doménu:port>*/.

* **Adresa URL odpovědi**: Adresa URL odpovědi u poskytovatele identity (IdP), která iniciuje přihlášení saml. Mapuje se na prvek **url odpovědi** ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*\<doménu:port>*/plugins/servlet/saml/auth.

* **Adresa URL přihlášení**: Adresa URL přihlášení v idp, která iniciuje přihlášení SAML. Mapuje se na prvek **Přihlášení** ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*\<doménu:port>*/plugins/servlet/saml/auth.

* **ID entity IdP**: ID entity, které používá idp. Toto pole je vyplněno při vyřešení adresy URL metadat.

* **Přihlašovací adresa URL**: Přihlašovací adresa URL od vašeho zástupce. Toto pole je naplněna z Azure AD při vyřešení adresy URL metadat.

* **Adresa URL odhlášení**: Adresa URL odhlášení od vašeho zástupce. Toto pole je naplněna z Azure AD při vyřešení adresy URL metadat.

* **Certifikát X.509**: Certifikát X.509 vašeho zástupce. Toto pole je naplněna z Azure AD při vyřešení adresy URL metadat.

* **Název přihlašovacího tlačítka**: Název přihlašovacího tlačítka, které vaše organizace chce, aby uživatelé viděli na přihlašovací stránce.

* **Umístění ID uživatele SAML**: Umístění, kde se očekává ID uživatele Jira nebo Confluence v odpovědi SAML. Může být v **NameID** nebo v názvu vlastního atributu.

* **Název atributu**: Název atributu, kde se očekává ID uživatele.

* **Povolit zjišťování domovské sféry**: Výběr, který má být uskutečněn, pokud společnost používá přihlášení služby AD FS (Active Directory Federation Services).

* **Název domény**: Název domény, pokud je přihlášení založeno na službě AD FS.

* **Povolit jednotné odhlášení:** Výběr, který chcete provést, pokud se chcete odhlásit z Azure AD, když se uživatel odhlásí z Jira nebo Confluence.

## <a name="troubleshooting"></a>Řešení potíží

* **Zobrazuje se vám několik chyb certifikátů**: Přihlaste se do Azure AD a odeberte více certifikátů, které jsou k dispozici pro aplikaci. Ujistěte se, že je k dispozici pouze jeden certifikát.

* **Certifikát brzy vyprší ve službě Azure AD**: Doplňky postarat o automatické převrácení certifikátu. Pokud vyprší platnost certifikátu, nový certifikát by měl být označen jako aktivní a nepoužité certifikáty by měly být odstraněny. Když se uživatel pokusí přihlásit k Jira v tomto scénáři, modul plug-in načte a uloží nový certifikát.

* **Chcete zakázat WebSudo (zakázat relaci zabezpečeného správce)**:

  * Pro Jiru jsou ve výchozím nastavení povoleny zabezpečené relace správce (tj. potvrzení hesla před přístupem k funkcím správy). Pokud chcete tuto schopnost odebrat v instanci Jira, zadejte do souboru jira-config.properties následující řádek:`ira.websudo.is.disabled = true`

  * V případě soutoku postupujte podle pokynů na [webu podpory confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pole, která mají být vyplněna adresou URL metadat, nejsou vyplněna**:

  * Zkontrolujte, zda je adresa URL správná. Zkontrolujte, jestli jste namapovali správné ID klienta a aplikace.

  * Zadejte adresu URL do prohlížeče a zjistěte, zda obdržíte federační metadata XML.

* **Došlo k vnitřní chybě serveru**: Zkontrolujte protokoly v adresáři protokolu instalace. Pokud se zobrazuje chyba, když se uživatel pokouší přihlásit pomocí služby Azure AD SSO, můžete sdílet protokoly s týmem podpory.

* **Při pokusu o přihlášení uživatele se chyba "ID uživatele nebylo nalezeno":** Vytvoření ID uživatele v Jira nebo Confluence.

* **Ve službě Azure AD se zobrazuje chyba "Aplikace nebyla nalezena":** Zjistěte, jestli je příslušná adresa URL namapována na aplikaci ve službě Azure AD.

* **Potřebujete podporu**: Oslovte [tým integrace přihlašovaného](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)k webu Azure AD . Tým reaguje v 24-48 pracovní době.

  Lístek podpory můžete také zvýšit s Microsoftem prostřednictvím kanálu portálu Azure.

## <a name="plug-in-faq"></a>Nejčastější dotazy k modulu plug-in

Pokud máte k tomuto modulu plug-in dotaz, přečtěte si níže nejčastější dotazy.

### <a name="what-does-the-plug-in-do"></a>K čemu modul plug-in dělá?

Modul plug-in poskytuje funkci jednotného přihlašování (SSO) pro místní software Atlassian Jira (včetně Jira Core, Jira Software, Jira Service Desk) a confluence. Modul plug-in funguje s Azure Active Directory (Azure AD) jako zprostředkovatel identity (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Se kterými produkty Atlassian modul plug-in pracuje?

Zásuvný modul pracuje s místními verzemi Jira a Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Funguje modul plug-in na cloudových verzích?

Ne. Modul plug-in podporuje pouze místní verze Jira a Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Které verze Jira a Confluence podporuje plug-in?

Modul plug-in podporuje tyto verze:

* Jira Core a software: 6,0 až 7,12
* Jira Service Desk: 3.0.0 až 3.5.0
* JIRA také podporuje 5.2. Další podrobnosti zobrazíte kliknutím [na jednotné přihlašování služby Microsoft Azure Active Directory pro JIRA 5.2.](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Soutok: 5,0 až 5,10
* Soutok: 6.0.1
* Soutok: 6.1.1
* Soutok: 6.2.1
* Soutok: 6.3.4
* Soutok: 6.4.0
* Soutok: 6.5.0
* Soutok: 6.6.2
* Soutok: 6.7.0
* Soutok: 6.8.1
* Soutok: 6.9.0
* Soutok: 6.10.0
* Soutok: 6.11.0
* Soutok: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Je plug-in zdarma nebo zaplacený?

Je to bezplatný doplněk.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Musím po nasazení modulu plug-in restartovat Jiru nebo Confluence?

Restartování není nutné. Zásuvný modul můžete začít používat okamžitě.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Jak získám podporu pro modul plug-in?

Můžete oslovit [tým integrace služby Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) pro všechny podpory potřebné pro tento modul plug-in. Tým reaguje v 24-48 pracovní době.

Lístek podpory můžete také zvýšit s Microsoftem prostřednictvím kanálu portálu Azure.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>By plug-in pracovat na Mac nebo Ubuntu instalace Jira a Confluence?

Plug-in jsme testovali pouze na 64bitových instalacích windows serveru Jira a Confluence.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Funguje modul plug-in s ip adresy než Azure AD?

Ne. Funguje pouze s Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>S jakou verzí programu SAML modul plug-in pracuje?

Pracuje s SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Zřazuje modul plug-in uživatele?

Ne. Modul plug-in poskytuje pouze přisychované přispojené přinásledujícím připojení založeném na saml 2.0. Uživatel musí být zřízena v aplikaci před přihlášení mj.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Podporuje modul plug-in verze clusteru Jira a Confluence?

Ne. Zásuvný modul pracuje s místními verzemi Jira a Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Funguje modul plug-in s HTTP verzemi Jiry a Confluence?

Ne. Modul plug-in funguje pouze s instalacemi s podporou protokolu HTTPS.
