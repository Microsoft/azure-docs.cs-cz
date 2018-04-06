---
title: Příručka správce pro Azure Active Directory jednotné přihlašování modulu plug-in | Microsoft Docs
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jira/soutoku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Příručka správce pro Azure Active Directory jednotné přihlašování modulu plug-in

## <a name="overview"></a>Přehled

Azure Active Directory (Azure AD) jednotné přihlašování (SSO) modul plug-in umožňuje zákazníkům Microsoft Azure AD pomocí svého pracovního nebo školního účtu pro přihlášení k Atlassian Jira a soutoku serverové produkty. Implementuje na základě SAML 2.0 SSO.

## <a name="how-it-works"></a>Jak to funguje

Pokud chtějí uživatelé přihlásit k aplikaci Atlassian Jira nebo soutoku, zobrazí se jim **přihlášení s Azure AD** tlačítko na přihlašovací stránce. Výběrem ji, jste nutné se přihlásit pomocí služby Azure AD organizace přihlašovací stránky (tedy svého pracovního nebo školního účtu).

Po ověření uživatele se mělo být možné se přihlásit k aplikaci. Pokud je již ověřen s ID a heslo pro svůj pracovní nebo školní účet, pak přímo přihlášení k aplikaci. 

Přihlášení funguje napříč Jira a soutoku. Pokud jsou uživatelé přihlášení k aplikaci Jira a soutoku se otevře v okně prohlížeče, nemají zadejte přihlašovací údaje pro jiné aplikace. 

Také se uživatelé dostanou do produktu Atlassian přes Moje aplikace v rámci pracovního nebo školního účtu. Musí být přihlášeni bez se zobrazí výzva k zadání pověření.

> [!NOTE]
> Zřizování uživatelů neprovádí prostřednictvím modulu plug-in.

## <a name="audience"></a>Cílová skupina

Jira a soutoku admins můžete použít modul plug-in pro povolení jednotného přihlašování pomocí služby Azure AD.

## <a name="assumptions"></a>Předpoklady

* Jira a soutoku instancí jsou povolené HTTPS.
* Uživatelé jsou už vytvořené v Jira nebo soutoku.
* Uživatelé mají role přiřazené v Jira nebo soutoku.
* Správci mají přístup k informacím, které jsou potřeba ke konfiguraci modulu plug-in.
* Jira nebo soutoku je k dispozici mimo do podnikové sítě.
* Modul plug-in pracuje s místní verzí Jira a soutoku.

## <a name="prerequisites"></a>Požadavky

Než nainstalujete modul plug-in, vezměte na vědomí následující informace:

* Jira a soutoku jsou nainstalovány na verzi 64bitovou verzi Windows.
* Verze Jira a soutoku nejsou povolené HTTPS.
* Jira a soutoku jsou dostupné na Internetu.
* Přihlašovací údaje správce jsou nastavené pro Jira a soutoku.
* Přihlašovací údaje správce jsou zavedené pro Azure AD.
* WebSudo je v Jira a soutoku zakázán.

## <a name="supported-versions-of-jira-and-confluence"></a>Podporované verze Jira a soutoku

Modul plug-in podporuje následující verze Jira a soutoku:

* Základní Jira a Software: 6.0 na 7.2.0
* Jira technickou podporu: 3.2 k 3.0
* Soutoku: 5.0 pro 5.10

## <a name="installation"></a>Instalace

Pokud chcete nainstalovat modul plug-in, postupujte takto:

1. Přihlaste se k instanci Jira nebo soutoku jako správce.
    
2. Přejděte na konzole pro správu Jira/soutoku a vyberte **doplňky**.
    
3. Vyhledejte v Marketplace Atlassian **zásuvný modul jednotné přihlašování SAML Microsoft**.
 
   Příslušnou verzi modulu plug-in se zobrazí ve výsledcích hledání.
 
5. Vyberte modul plug-in, a univerzální Plug-in správce (UPM) ho nainstaluje.
 
Po instalaci modulu plug-in se zobrazí v **uživatele nainstalovat doplňky** části **spravovat doplňky**.
    
## <a name="plug-in-configuration"></a>Konfigurace modulu plug-in

Než začnete používat modul plug-in, musíte ho nakonfigurovat. Modul plug-in, vyberte **konfigurace** tlačítko a zadejte podrobnosti o konfiguraci.

Následující obrázek ukazuje na obrazovce konfigurace v Jira i soutoku:
    
![Konfigurace modulu plug-in obrazovky](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Adresa URL metadat**: adresa URL federačních metadat získat ze služby Azure AD.
 
*   **Identifikátory**: adresa URL, že Azure AD se používá k ověření zdroj žádosti. Je mapován **identifikátor** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/.
 
*   **Adresa URL odpovědi**: adresa URL odpovědi v rámci poskytovatele identity (IdP), který iniciuje přihlášení SAML. Je mapován **adresa URL odpovědi** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/plugins/servlet/saml/auth.
 
*   **Přihlašovací adresa URL**: adresu URL přihlašování vaší deklarací identity, který iniciuje přihlášení SAML. Je mapován **přihlašování** element ve službě Azure AD. Modul plug-in automaticky odvozuje tuto adresu URL jako https://*< domény: port >*/plugins/servlet/saml/auth.
 
*   **IdP Entity ID**: ID entity, který používá vaše IdP. Toto pole se zaplní při URL adresu metadat je vyřešený.
 
*   **Adresa URL pro přihlášení**: adresu URL přihlášení z vaší deklarací identity. Toto pole se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
*   **Adresa URL odhlašovací**: adresa URL odhlašovací z vaší IdP. Toto pole se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
*   **Certifikát X.509**: vaše IdP certifikát X.509. Toto pole se naplní ze služby Azure AD, když je adresa URL metadat vyřešený.
 
*   **Název tlačítka přihlášení**: název tlačítka přihlášení, které vaše organizace chce, aby se uživatelům zobrazit na stránce přihlášení.
 
*   **Umístění ID uživatele SAML**: umístění, kde je očekávána Jira nebo soutoku ID uživatele v odpovědi SAML. Může být v **NameID** nebo název vlastního atributu.
 
*   **Atribut název**: název atributu, kde je očekávána ID uživatele.
 
*   **Povolit zjišťování domovské sféry**: výběr, ujistěte se, pokud společnost používá služby Active Directory Federation Services (AD FS) - základě sign - v.
 
*   **Název domény**: název domény, pokud se přihlášení je na základě služby AD FS.
 
*   **Povolit jednu odhlášení**: Ujistěte se, pokud chcete odhlásit z Azure AD, když uživatel odhlásí od Jira nebo soutoku výběr.

## <a name="troubleshooting"></a>Řešení potíží

* **Vám několik chyb certifikát**: Přihlaste se k Azure AD a odeberte více certifikátů, které jsou k dispozici pro aplikaci. Zajistěte, že pouze jeden certifikát je k dispozici.

* **Certifikát má vypršet ve službě Azure AD**: doplňky postará o automatické výměny certifikátu. Pokud je certifikát vyprší, nový certifikát by měl být označen jako aktivní a měla by být odstraněna nepoužívané certifikáty. Když uživatel se pokusí přihlásit k Jira v tomto scénáři, modul plug-in načte a uloží nový certifikát.

* **Chcete zakázat WebSudo (zakázat relace zabezpečené správce)**:
    
  * Pro Jira jsou ve výchozím nastavení povolené zabezpečené Správce relace (tedy potvrzení hesla před přístupem k funkcím pro správu). Pokud chcete odebrat tuto možnost v Jira instanci, zadejte následující řádek v souboru jira config.properties: `ira.websudo.is.disabled = true`
    
  * Soutoku, postupujte podle kroků [soutoku podporu lokality](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Pole, které by mohly být obsazeny URL adresu metadat nejsou získávání naplněny**:
    
  * Zkontrolujte, jestli je adresa URL správná. Zkontrolujte, pokud jsou namapovány správné ID klienta a aplikace.
    
  * Zadejte adresu URL v prohlížeči a zobrazit, pokud se zobrazí federačních metadat XML.

* **Je o vnitřní chybu serveru**: Zkontrolujte protokoly v adresáři protokolu instalace. Pokud chyba vám při pokusu uživatele o přihlašování pomocí jednotného přihlašování Azure AD, můžete protokoly sdílet s tým podpory.

* **Když se uživatel pokusí přihlásit dochází k chybě "ID uživatele nebyla nalezena"**: Vytvořte ID uživatele v Jira nebo soutoku.

* **Dojde k chybě "Aplikace nebyla nalezena" ve službě Azure AD**: viz, pokud příslušnou adresou URL je namapovaný na aplikaci ve službě Azure AD.

* **Potřebujete podporu**: přístup ke [týmu Integrace Azure AD jednotného přihlašování k](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Tým odpovídá v 24 48 pracovní dobu.
    
  Můžete taky zvýšit lístek podpory se společností Microsoft prostřednictvím portálu Azure kanálu.