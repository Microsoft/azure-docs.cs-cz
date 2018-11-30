---
title: Příručka pro správce od společnosti Atlassian Jira/Confluence – Azure Active Directory | Dokumentace Microsoftu
description: Příručka pro správce pro použití s Azure Active Directory (Azure AD) od společnosti Atlassian Jira a Confluence...
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 65649c3fdc11d74b5888b26b81ae85e10f2788bd
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426872"
---
# <a name="atlassian-jira-and-confluence-admin-guide-for-azure-active-directory"></a>Od společnosti Atlassian Jira a Confluence příručky pro správce Azure Active Directory

## <a name="overview"></a>Přehled

Azure Active Directory (Azure AD) jednotné přihlašování (SSO) modul plug-in umožňuje zákazníkům Microsoft Azure AD používat svůj pracovní nebo školní účet pro přihlášení k Atlassian Jira a Confluence serverových produktů. Implementuje jednotné přihlašování založené na SAML 2.0.

## <a name="how-it-works"></a>Jak to funguje

Když uživatelé mají k přihlášení do aplikace od společnosti Atlassian Jira nebo Confluence, zobrazí se mu **přihlášení pomocí Azure AD** tlačítko na přihlašovací stránku. Když vyberou ji, musí se přihlásit pomocí Azure AD organizace přihlašovací stránky (to znamená, že jejich pracovní nebo školní účet).

Po ověření uživatele, třeba moct přihlásit k aplikaci. Pokud je již ověřen s ID a heslo ke svému pracovnímu nebo školnímu účtu, potom přímo přihlášení k aplikaci. 

Přihlašování funguje napříč Jira a Confluence. Pokud jsou uživatelé přihlášení do aplikace systému Jira a ve stejném okně prohlížeče se otevře Confluence, nemají zadejte přihlašovací údaje pro jinou aplikaci. 

Také se uživatelé dostanou na produkt od společnosti Atlassian prostřednictvím Moje aplikace v rámci pracovního nebo školního účtu. By měl být přihlášeni bez požádaná, pro přihlašovací údaje.

> [!NOTE]
> Zřizování uživatelů se provádí prostřednictvím modulu plug-in.

## <a name="audience"></a>Cílová skupina

Správci systému Jira a Confluence můžete použít modul plug-in pro povolení jednotného přihlašování pomocí Azure AD.

## <a name="assumptions"></a>Předpoklady

* Jira a Confluence instance jsou povolené HTTPS.
* Uživatelé jsou už vytvořené v systému Jira nebo Confluence.
* Uživatelé mají přiřazené v systému Jira nebo Confluence role.
* Správci mají přístup k informacím, které jsou potřeba ke konfiguraci modulu plug-in.
* Jira nebo Confluence je k dispozici mimo podnikovou síť.
* Modul plug-in funguje s místní verzí systému Jira a Confluence.

## <a name="prerequisites"></a>Požadavky

Než nainstalujete modul plug-in, mějte na paměti následující informace:

* Jira a Confluence jsou nainstalovány na verzi Windows 64-bit.
* Jira a Confluence verze jsou povolené HTTPS.
* Jira a Confluence jsou dostupné na Internetu.
* Přihlašovací údaje správce jsou nastavené pro Jira a Confluence.
* Přihlašovací údaje správce jsou na místě pro službu Azure AD.
* WebSudo je v systému Jira a Confluence zakázaná.

## <a name="supported-versions-of-jira-and-confluence"></a>Podporované verze systému Jira a Confluence

Modul plug-in podporuje následující verze systému Jira a Confluence:

* Základní Jira a Software: 6.0 k 7.12
* Oddělení služeb Jira: 3.0.0 k 3.5.0
* JIRA podporuje také 5.2. Další podrobnosti získáte kliknutím [Microsoft Azure Active Directory jednotného přihlašování pro JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 k 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

## <a name="installation"></a>Instalace

Pokud chcete nainstalovat modul plug-in, postupujte takto:

1. Přihlaste se k vaší instanci Jira nebo Confluence jako správce.

2. Přejděte do konzoly pro správu systému Jira/Confluence a vyberte **doplňky**.

3. Stáhnout z webu Microsoft Download Center [modulu plug-in Microsoft SAML jednotného přihlašování pro Jira](https://www.microsoft.com/download/details.aspx?id=56506)/ [modulu plug-in Microsoft SAML jednotného přihlašování pro Confluence](https://www.microsoft.com/download/details.aspx?id=56503).

   Příslušnou verzi modulu plug-in se zobrazí ve výsledcích hledání.

4. Vyberte modul plug-in a univerzální modul Plug-in Manager (UPM) nainstaluje ho.

Po instalaci modulu plug-in se zobrazí v **nainstalovaná doplňky uživatele** část **spravovat doplňky**.

## <a name="plug-in-configuration"></a>Konfigurace modulu plug-in

Než začnete používat modul plug-in, je nutné nakonfigurovat. Modul plug-in, vyberte **konfigurovat** tlačítko a zajištění podrobných konfiguračních informací.

Na obrazovce pro konfiguraci v systému Jira a Confluence na následujícím obrázku:

![Obrazovka Konfigurace modulu plug-in](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Adresa URL metadat**: adresa URL federačních metadat získat ze služby Azure AD.

*   **Identifikátory**: adresa URL služby Azure AD používá ověřit zdroj požadavku. Mapuje **identifikátor** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/.

*   **Adresa URL pro odpověď**: adresa URL odpovědi ve zprostředkovateli identity (IdP), která inicializuje přihlášení SAML. Mapuje **adresy URL odpovědi** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/plugins/servlet/saml/auth.

*   **Přihlašovací adresa URL**: adresu URL přihlašování svého zprostředkovatele identity, který iniciuje přihlášení SAML. Mapuje **přihlašování** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/plugins/servlet/saml/auth.

*   **IdP Entity ID**: ID entity, která používá svého zprostředkovatele identity. Toto pole se vyplní když adresa URL metadat je vyřešený.

*   **Adresa URL pro přihlášení**: adresu URL přihlásit ze svého zprostředkovatele identity. Toto pole se naplní ze služby Azure AD, pokud je adresa URL metadat vyřešit.

*   **Odhlašovací adresa URL**: adresu URL pro odhlášení z svého zprostředkovatele identity. Toto pole se naplní ze služby Azure AD, pokud je adresa URL metadat vyřešit.

*   **Certifikát X.509**: certifikát X.509 vašeho zprostředkovatele identity. Toto pole se naplní ze služby Azure AD, pokud je adresa URL metadat vyřešit.

*   **Přihlašovací jméno tlačítko**: název přihlašovací tlačítko, které vaše organizace chce, aby se uživatelům zobrazit na přihlašovací stránku.

*   **Umístění ID uživatele SAML**: umístění, kde se očekává ID uživatele Jira nebo Confluence odpověď SAML. Může být v **NameID** nebo v názvu vlastního atributu.

*   **Atribut název**: název atributu, kde se očekává ID uživatele.

*   **Povolit zjišťování domovské sféry**: výběr, ujistěte se, pokud společnost používá služby Active Directory Federation Services (AD FS) - based sign - v.

*   **Název domény**: název domény při přihlášení na základě služby AD FS.

*   **Povolit jednotné odhlášení**: Ujistěte se, pokud se chcete odhlásit se ze služby Azure AD při odhlášení uživatele ze systému Jira nebo Confluence výběru.

## <a name="troubleshooting"></a>Řešení potíží

* **Více chyby certifikátu**: Přihlaste se k Azure AD a odebírat více certifikátů, které jsou k dispozici pro aplikaci. Ujistěte se, že je k dispozici pouze jeden certifikát.

* **Certifikátu brzy vyprší platnost ve službě Azure AD**: doplňky postará o automatické výměny certifikátu. Pokud certifikátu brzy vyprší platnost, nový certifikát by měl být označena jako aktivní a nepoužívané certifikáty by měly být odstraněny. Když uživatel se pokusí přihlásit do nástroje Jira v tomto scénáři, modul plug-in načte a uloží nový certifikát.

* **Chcete zakázat WebSudo (zakázání relace zabezpečení správce)**:

  * Pro Jira jsou ve výchozím nastavení povoleno zabezpečené Správce relací (to znamená, potvrzení hesla před přístupem k funkcím pro správu). Pokud chcete odebrat tuto možnost v instanci systému Jira, zadejte následující řádek v souboru jira config.properties: `ira.websudo.is.disabled = true`

  * Confluence, postupujte podle pokynů [web podpory Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pole, která mají být vyplněn adresa URL metadat získávání naplněny**:

  * Zkontrolujte, jestli je adresa URL správná. Zaškrtněte, pokud jste namapovali správné ID tenanta a aplikace.

  * Zadejte adresu URL v prohlížeči a zobrazit, pokud se zobrazí kód XML metadat federace.

* **Je interní chybu serveru**: Zkontrolujte protokoly v adresáři protokolu instalace. Pokud dochází k chybě při pokusu uživatele o přihlášení pomocí jednotného přihlašování k Azure AD, můžete protokoly sdílet se na tým podpory.

* **Když se uživatel pokusí přihlásit dochází k chybě "ID uživatele nebyl nalezen"**: vytvořit ID uživatele v systému Jira nebo Confluence.

* **Ve službě Azure AD je chyba "Aplikace nebyla nalezena"**: zobrazit, pokud příslušné adrese URL se mapuje na aplikaci ve službě Azure AD.

* **Potřebujete podporu**: oslovení [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Tým odpovídá ve 24 nebo 48 pracovní doby.

  Můžete také zvýšit lístek podpory s Microsoftem přes Azure portal kanál.

## <a name="plug-in-faq"></a>Modul plug-in – nejčastější dotazy

Pokud máte dotazy ohledně této najdete pod nejčastější dotazy k modulu plug-in.

### <a name="what-does-the-plug-in-do"></a>Co dělá modulu plug-in?

Modul plug-in poskytuje jednotné přihlašování (SSO) od společnosti Atlassian Jira (včetně jádra Jira, rozšiřují Software jira, Jira oddělení služeb) a Confluence u místního softwaru. Modul plug-in funguje s Azure Active Directory (Azure AD) jako zprostředkovatele identity (IdP).

### <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Které produkty od společnosti Atlassian modul plug-in funguje s?

Modul plug-in funguje s místní verzí systému Jira a Confluence.

### <a name="does-the-plug-in-work-on-cloud-versions"></a>Modul plug-in funguje v cloudu verze?

Ne. Modul plug-in podporuje jenom místní verze systému Jira a Confluence.

### <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Které verze systému Jira a Confluence podporuje modul plug-in podporu?

Modul plug-in podporuje tyto verze:

* Základní Jira a Software: 6.0 k 7.12
* Oddělení služeb Jira: 3.0.0 k 3.5.0
* JIRA podporuje také 5.2. Další podrobnosti získáte kliknutím [Microsoft Azure Active Directory jednotného přihlašování pro JIRA 5.2](https://docs.microsoft.com/azure/active-directory/saas-apps/jira52microsoft-tutorial)
* Confluence: 5.0 k 5.10
* Confluence: 6.0.1
* Confluence: 6.1.1
* Confluence: 6.2.1
* Confluence: 6.3.4
* Confluence: 6.4.0
* Confluence: 6.5.0
* Confluence: 6.6.2
* Confluence: 6.7.0
* Confluence: 6.8.1
* Confluence: 6.9.0
* Confluence: 6.10.0
* Confluence: 6.11.0
* Confluence: 6.12.0

### <a name="is-the-plug-in-free-or-paid"></a>Je modul plug-in bezplatné nebo placené?

Je bezplatný doplněk.

### <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Je potřeba restartovat Jira nebo Confluence nasadit modul plug-in?

Restartování se nevyžaduje. Chcete-li začít, použitím modulu plug-in okamžitě.

### <a name="how-do-i-get-support-for-the-plug-in"></a>Jak získat podporu pro modul plug-in?

Abyste se obrátili na [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) jakoukoli podporu v případě pro tento modul plug-in. Tým odpovídá ve 24 nebo 48 pracovní doby.

Můžete také zvýšit lístek podpory s Microsoftem přes Azure portal kanál.

### <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Modul plug-in práce na Mac nebo Ubuntu instalaci systému Jira a Confluence by?

Jsme testovali jenom v 64bitových instalacích systému Windows Server ze systému Jira a Confluence modulu plug-in.

### <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Modul plug-in funguje s zprostředkovatelů identity než Azure AD?

Ne. Funguje pouze s Azure AD.

### <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Jakou verzi SAML modul plug-in funguje s?

Funguje to SAML 2.0.

### <a name="does-the-plug-in-do-user-provisioning"></a>Modul plug-in dělá zřizování uživatelů?

Ne. Modul plug-in obsahuje pouze na základě SAML 2.0 SSO. Uživatel se musí zřídit v aplikaci před přihlášení SSO.

### <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Podporuje verze modulu plug-in podpora clusteru Jira a Confluence?

Ne. Modul plug-in funguje s místní verzí systému Jira a Confluence.

### <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Modul plug-in funguje s verzemi HTTP Jira a Confluence?

Ne. Modul plug-in funguje s povolenou komunikací HTTPS pouze zařízení.