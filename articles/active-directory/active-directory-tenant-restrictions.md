---
title: Správa přístupu k cloudových aplikací tak, že omezíte klienti – Azure | Microsoft Docs
description: Postup použití omezení klienta ke správě uživatelů, kteří mají přístup k aplikace založené na jejich klienta Azure AD.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: yossib
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2018
ms.author: kgremban
ms.openlocfilehash: a6b498b38e76dfa2553bf3a916b723cd774d950d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Omezení použití klienta můžete spravovat přístup k SaaS cloudové aplikace.

Velké organizace, které zdůraznil zabezpečení chcete přesunout do cloudové služby, jako je Office 365, ale potřebujete vědět, že jejich pouze mohou uživatelé schválené prostředky. Společností tradičně, omezení názvů domény nebo IP adresy, když chtějí spravovat přístup. Tento postup selže ve světě, kde jsou hostované aplikace SaaS ve veřejném cloudu, na názvy sdílených domén jako outlook.office.com a login.microsoftonline.com spuštěna. Blokování tyto adresy by zabránit uživatelům v přístupu k aplikaci Outlook na webu zcela, místo prostého je omezení schválené identit a prostředky.

Řešení Azure Active Directory je pro tento problém je funkci omezení klienta. Omezení klienta umožňuje organizacím řídit přístup k aplikacím, cloudové SaaS, podle klienta Azure AD, které aplikace použít pro jednotné přihlašování. Chcete třeba povolit přístup k aplikacím Office 365 vaší organizace a zároveň je možné zabránit přístupu do jiných organizací instancí těchto stejné aplikace.  

Omezení klienta poskytuje organizacím umožňuje určit seznam klientů, které mají uživatelé přístup. Azure AD poté pouze uděluje přístup do těchto povolené klienty.

Tento článek zaměřuje na omezení klienta pro Office 365, ale tato funkce by měly spolupracovat s všech cloudových aplikací SaaS, která používá moderní ověřování protokoly s Azure AD pro jednotné přihlašování. Pokud používáte SaaS aplikace s jinou Azure AD klienta z klienta, používá Office 365, ujistěte se, že všechny požadované, klienti jsou povoleny. Další informace o cloudových aplikací SaaS, najdete v článku [Active Directory Marketplace](https://azure.microsoft.com/en-us/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Celkového řešení obsahuje následující součásti: 

1. **Azure AD** – Pokud `Restrict-Access-To-Tenants: <permitted tenant list>` je přítomen, Azure AD pouze problémy tokeny zabezpečení klientům povolené. 

2. **Místní proxy server infrastruktury** – schopná kontrolu SSL, nakonfigurován tak, aby vložit záhlaví obsahující seznam zařízení proxy povoleno klienty do provoz určený pro Azure AD. 

3. **Klientský software** – k podpoře klienta omezení, klientský software musí požádat tokeny přímo ze služby Azure AD, tak, aby provoz může být zachycen infrastruktury služby proxy. Omezení klienta se aktuálně podporuje založené na prohlížeči aplikace Office 365 a klienti Office při použití moderní ověřování (např. OAuth 2.0). 

4. **Moderní ověřování** – cloudové služby musíte použít moderní ověřování používají omezení klienta a zablokovat přístup pro všechny klienty není povolené. Office 365 cloudové služby je nutné nakonfigurovat moderní ověřování protokoly ve výchozím nastavení. Nejnovější informace o podporovaných Office 365 pro moderní ověřování, přečtěte si [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Následující diagram znázorňuje tok vysoké úrovně přenosů. Kontrolu SSL je vyžadováno pouze u veškerý provoz do služby Azure AD, ne ke cloudovým službám Office 365. Tento rozdíl je důležitý, protože objem provozu pro ověřování do služby Azure AD je obvykle mnohem nižší než objem provozu pro aplikace SaaS, jako je Exchange Online a SharePoint Online.

![Klienta omezení přenosový tok – diagram](./media/active-directory-tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení klienta

Existují dva kroky, jak začít s omezení klienta. Prvním krokem je zajistit, že vaši klienti mohou připojit ke správné adresy. Druhá je konfigurace proxy serveru infrastruktury.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Pokud chcete použít klienta omezení, musí být vaši klienti moci připojit k následujícím adresám URL Azure AD ověřit: login.microsoftonline.com, login.microsoft.com a login.windows.net. Kromě toho přístup k Office 365, klienti musí být také možné se připojit k plně kvalifikovaný název domény adresy URL a IP adresy definované v [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace proxy serveru a požadavky

Následující konfigurace je potřeba povolit omezení přístupu klientů prostřednictvím infrastruktury proxy serveru. V tomto návodu je obecný, takže naleznete v dokumentaci dodavatele proxy pro konkrétní implementaci.

#### <a name="prerequisites"></a>Požadavky

- Proxy server musí být schopen provést zachycením SSL, vkládání hlavičky protokolu HTTP a filtrovat pomocí plně kvalifikovaných názvů domény nebo adresy URL cíle. 

- Klienti musí důvěřovat řetězu certifikátů, které jsou prezentované podle proxy serveru pro komunikaci SSL. Například pokud se používají certifikáty z interní infrastruktury veřejných KLÍČŮ, interní vydávající certifikát kořenové certifikační autority musí být důvěryhodný.

- Tato funkce je zahrnutá v předplatná Office 365, ale pokud chcete použít k řízení přístupu do jiných aplikací SaaS omezení klienta jsou nutné pak licence Azure AD Premium 1.

#### <a name="configuration"></a>Konfigurace

Pro každého příchozího požadavku login.microsoftonline.com, login.microsoft.com a login.windows.net vložit dvě hlavičky protokolu HTTP: *omezit přístup k klienty* a *omezit-Access-kontext*.

Hlavičky by měla obsahovat tyto prvky: 
- Pro *omezit přístup k klienty*, hodnota \<povolené klienta seznamu\>, což je textový soubor s oddělovači seznam klientů, které chcete povolit uživatelům přístup k. Do libovolné domény, která je zaregistrovaná u klienta slouží k identifikaci klienta v tomto seznamu. Chcete-li povolit přístup klientům Contoso a Fabrikam, například dvojici název – hodnota vypadá takto:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Pro *omezit-Access-kontext*, hodnotu a ID jednoho adresářového deklarace, které klient je nastavení omezení klienta. Deklarovat Contoso jako klienta, který nastavit zásady omezení klienta, například dvojici název – hodnota vypadá takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Vaše ID adresáře v můžete najít [portál Azure](https://portal.azure.com). Přihlaste se jako správce, vyberte **Azure Active Directory**, pak vyberte **vlastnosti**.

Uživatelům zabránit v vkládání vlastní hlavičku HTTP s neschválené klienty, je potřeba nahradit hlavičku omezit přístup pro klienty, pokud se již nachází v příchozím požadavku k proxy serveru. 

Klienti musí být nuceni používat proxy server pro všechny požadavky na login.microsoftonline.com, login.microsoft.com a login.windows.net. Například pokud PAC soubory se používají k nasměrování klientů na použít proxy server, koncoví uživatelé by neměl moci upravit nebo zakázat PAC soubory.

## <a name="the-user-experience"></a>Činnost koncového uživatele

Tato část uvádí prostředí pro koncové uživatele a správce.

### <a name="end-user-experience"></a>Činnost koncového uživatele

Uživatel s příklad je v síti Contoso, ale se pokouší získat přístup k instanci Fabrikam sdílené SaaS aplikace jako je Outlook online. Pokud Contoso-povolené klienta pro tuto instanci, uživateli se zobrazí následující stránka:

![Přístup odepřen stránku pro uživatele v klientech – povoleno](./media/active-directory-tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Z pohledu správce

Během konfigurace omezení klienta se provádí na infrastruktuře podnikový proxy server, mohou správci přímý přístup omezení klienta sestav na portálu Azure. Chcete-li zobrazit sestavy, přejděte na stránku Přehled služby Azure Active Directory a potom podívejte se do části Další možnosti.

ID správce pro tenanta zadaný jako s omezeným přístupem-Access-kontext klienta můžete pomocí této sestavy zobrazíte všechny přihlášení blokovaný z důvodu omezení klienta zásad, včetně identity použité a cílové složky.

![Chcete-li zobrazit s omezeným přístupem pokusů o přihlášení pomocí portálu Azure](./media/active-directory-tenant-restrictions/portal-report.png)

Jako další sestav na portálu Azure můžete použít filtry k určení oboru sestavy. Můžete filtrovat podle konkrétního uživatele, aplikace, klient nebo časovém intervalu.

## <a name="office-365-support"></a>Podpora Office 365

Aplikace Office 365, musí splňovat dvě kritéria pro zajištění plné podpory omezení klienta:

1. Podporuje klient používá moderní ověřování
2. Jako výchozí ověřovací protokol pro cloudové služby je povolené moderní ověřování.

Odkazovat na [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) nejnovější informace, na které Office klienti v současné době podporují moderní ověřování. Této stránce taky obsahuje odkazy na pokyny pro povolení moderní ověřování na konkrétní Exchange Online a Skype for Business Online klientů. Ve výchozím nastavení ve službě SharePoint Online je již povolené moderní ověřování.

Omezení klienta v současné době podporuje aplikace založené na prohlížeči Office 365 (služby SharePoint portálu Office, Yammer, lokality, Outlook Web, např.). Pro silné klienty (Outlook, Skype pro firmy, Word, Excel, PowerPoint, atd.) Omezení klienta můžete vynutit pouze, pokud se používá moderní ověřování.  

Outlook a Skype pro firmy klienty, kteří podporují moderní ověřování může stále moci používat starších protokolů pro klienty, kde není povolené moderní ověřování, efektivně obcházení omezení klienta. Aplikace, které používají starší verze protokoly mohou být blokovány omezení klienta, pokud kontaktovat login.microsoftonline.com, login.microsoft.com nebo login.windows.net během ověřování.

Pro aplikaci Outlook v systému Windows zákazníci rozhodnout k implementaci omezení brání koncovým uživatelům v přidávání neschválený e-mailové účty do jejich profilů. Například najdete v článku [zabránit přidání jiné než výchozí účty serveru Exchange](http://gpsearch.azurewebsites.net/default.aspx?ref=1) nastavení zásad skupiny. Pro aplikaci Outlook na platformách systému Windows a pro Skype pro firmy na všech platformách není aktuálně k dispozici plnou podporu pro omezení klienta.

## <a name="testing"></a>Testování

Pokud chcete vyzkoušet omezení klienta před implementací pro celou organizaci, máte dvě možnosti: použití metody založené na hostiteli pomocí nástroje, například aplikaci Fiddler nebo dvoufázové instalace zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler přístup, založené na hostiteli

Fiddler je bezplatných webových ladění proxy server, který slouží k zaznamenání a upravit provoz protokolu HTTP nebo HTTPS, včetně vkládání hlavičky protokolu HTTP. Pokud chcete nakonfigurovat aplikaci Fiddler k testování omezení klienta, proveďte následující kroky:

1.  [Stáhněte a nainstalujte aplikaci Fiddler](http://www.telerik.com/fiddler).
2.  Nakonfigurujte aplikaci Fiddler k dešifrování komunikaci přes protokol HTTPS, za [nápovědě k aplikaci Fiddler na](http://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Nakonfigurujte aplikaci Fiddler k vložení *omezit přístup k klienty* a *omezit-Access-kontext* hlavičky pomocí vlastní pravidla:
  1. V nástroji ladicí program webové aplikaci Fiddler, vyberte **pravidla** nabídku a vyberte **přizpůsobit pravidla...** k otevření souboru CustomRules.
  2. Přidejte následující řádky na začátek *OnBeforeRequest* funkce. Nahraďte \<doména klienta\> s doménou zaregistrována vašeho klienta, například contoso.onmicrosoft.com. Nahraďte \<ID adresáře\> s identifikátorem Azure AD GUID vašeho klienta.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Pokud je potřeba povolit více klientů, oddělte názvy klienta pomocí čárkou. Příklad:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Uložte a zavřete soubor CustomRules.

Po dokončení konfigurace aplikaci Fiddler, můžete zaznamenávat provoz, přechodem na **soubor** nabídky a výběrem **zachycování provozu**.

### <a name="staged-rollout-of-proxy-settings"></a>Dvoufázové instalace zavedení nastavení proxy serveru

V závislosti na možnosti infrastruktury proxy nebudete moct dvoufázové nasazení pro nastavení pro vaše uživatele. Zde jsou základní možnosti několik důvodů:

1.  Použijte PAC soubory tak, aby odkazoval testovací uživatele infrastruktury služby proxy test při normálním uživatelé nadále používat infrastruktury služby proxy produkční.
2.  Některé servery proxy může podporovat různé konfigurace pomocí skupin.

Naleznete v dokumentaci k proxy serveru pro konkrétní podrobnosti.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Zkontrolujte [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
