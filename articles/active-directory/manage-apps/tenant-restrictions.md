---
title: Správa přístupu ke cloudovým aplikacím tak, že omezíte klienti – Azure | Dokumentace Microsoftu
description: Jak používat exportní Tenanta pro správu, které uživatelé můžou používat aplikace na základě jejich tenantů Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: yossib
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: barbkess
ms.reviewer: richagi
ms.openlocfilehash: ca3396024fdab31217b00ef7cdd39e4e61504d27
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52851090"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Omezení Tenanta můžete spravovat přístup k SaaS cloudové aplikace

Velké organizace, které zdůrazňují zabezpečení chcete přesunout do cloudové služby, jako je Office 365, ale třeba vědět, že jejich pouze mohou uživatelé schválené prostředky. Společnosti tradičně, omezení názvů domény nebo IP adres, ji můžete spravovat přístup. Tento přístup se ve světě, kde jsou hostované aplikace SaaS ve veřejném cloudu, které běží na názvy sdílená doména jako outlook.office.com a login.microsoftonline.com nezdaří. Blokování tyto adresy byste zabránit uživatelům v přístupu k aplikaci Outlook na webu úplně, namísto pouze omezení schválené identity a prostředky.

Azure Active Directory pro řešení těchto problémů je funkce volána exportní Tenanta. Omezení tenanta umožňuje organizacím řídit přístup ke cloudovým aplikacím SaaS, založené na tenanta Azure AD, které aplikace se používají pro jednotné přihlašování. Chcete například povolit přístup k aplikacím Office 365 vaší organizace, zároveň je možné zabránit přístupu do jiných organizací instancí tyto stejné aplikace.  

Omezení tenanta poskytuje organizacím možnost zadat seznam klientů, které mají povolen přístup k svým uživatelům. Azure AD pak pouze uděluje přístup k těmto klientům povoleno.

Tento článek se zaměřuje na klienta omezení pro Office 365, ale tato funkce by měla fungovat s žádnou cloudovou aplikaci SaaS, která používá moderní ověřování protokoly s Azure AD pro jednotné přihlašování. Pokud používáte SaaS aplikací pomocí jiné služby Azure AD tenanta z tenanta používaný v Office 365, ujistěte se, že všechny požadované jsou klientům povolené. Další informace o cloudových aplikacích SaaS, najdete v článku [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Celkové řešení se skládá z následujících součástí: 

1. **Azure AD** – Pokud `Restrict-Access-To-Tenants: <permitted tenant list>` je k dispozici, Azure AD pouze problémy s tokeny zabezpečení pro povolených tenantů. 

2. **Místní proxy server infrastruktury** – dokáže kontroly protokolu SSL, nakonfigurován tak, aby vložit záhlaví obsahující seznam proxy zařízení povolené tenantů do provozu určeného pro službu Azure AD. 

3. **Klientský software** – podporovat exportní Tenanta, klientský software musí požádat o tokeny přímo ze služby Azure AD tak, aby provoz může být zachycen infrastruktury proxy serveru. Exportní tenanta aktuálně podporuje aplikace využívající prohlížeč Office 365 a klienti Office při používá moderní ověřování (jako jsou OAuth 2.0). 

4. **Moderní ověřování** – cloudové služby musí používat omezení klienta a zablokovat přístup pro všechny tenanty bez povolené používají moderní ověřování. Office 365 cloudové služby musí být ve výchozím nastavení nakonfigurován pro použití moderních ověřovacích protokolů. Nejnovější informace o podpoře služeb Office 365 pro moderní ověřování, přečtěte si [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/).

Následující diagram znázorňuje tok provozu vysoké úrovně. Kontrola protokolu SSL je vyžadováno pouze u provozu do služby Azure AD, nikoli do cloudové služby Office 365. Tento rozdíl je důležitý, protože objem přenosů pro ověření do služby Azure AD je obvykle mnohem nižší než objem provozu pro aplikace SaaS, jako je Exchange Online a SharePoint Online.

![Tenant omezení toku provozu – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení Tenanta

Existují dva kroky, abyste mohli začít s omezeními pro Tenanta. Prvním krokem je chcete mít jistotu, že vaši klienti můžou připojit k správné adresy. Druhým je konfigurace vaší infrastruktury služby proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Pokud chcete použít exportní Tenanta, musí být schopný se připojit k následujícím adresám URL Azure AD k ověření vaši klienti: login.microsoftonline.com login.microsoft.com a login.windows.net. Kromě toho pro přístup k Office 365, klienti musí být také moci připojit k plně kvalifikované názvy domény adresy URL a IP adresy definované v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace proxy serveru a požadavky

Aktivaci Tenanta omezení prostřednictvím vaší infrastruktury služby proxy je nutná tato konfigurace. Tyto pokyny je obecný, takže by měla odkazovat na dokumentaci od dodavatele proxy server pro konkrétní implementaci.

#### <a name="prerequisites"></a>Požadavky

- Proxy server musí být schopen provést SSL zachycení, HTTP záhlaví vložení a filtrovat pomocí plně kvalifikovaných názvů domén a adres URL cíle. 

- Klienti musí důvěřovat řetězu certifikátů, které jsou prezentované podle proxy serveru k zajištění komunikace SSL. Například pokud se používají certifikáty z interní infrastruktury veřejných KLÍČŮ, interní vydávající certifikát kořenové certifikační autority musí být důvěryhodný.

- Tato funkce je zahrnutá v předplatných Office 365, ale pokud chcete používat Tenanta omezení řízení přístupu k jiným aplikacím SaaS pak licence Azure AD Premium 1 se vyžadují.

#### <a name="configuration"></a>Konfigurace

Pro každého příchozího požadavku login.microsoftonline.com login.microsoft.com a login.windows.net, vložit dvě záhlaví HTTP: *omezit přístup pro klienty* a *omezit přístup kontextu*.

Záhlaví by měl obsahovat následující prvky: 
- Pro *omezit přístup pro klienty*, hodnota \<povolené seznamu v tenantovi\>, což je čárkou oddělený seznam klientů, které chcete povolit uživatelům přístup k. Do libovolné domény, který je registrovaný pomocí tenanta můžete použít k identifikaci tenanta služby v tomto seznamu. Například tak, aby povolovala přístup pro klienty Contoso a Fabrikam, dvojice název/hodnota vypadá takto:  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com` 
- Pro *omezit přístup kontextu*, hodnotu ID jednoho adresáře, deklarace, kterého tenanta je nastavení Tenanta omezení. Chcete-li deklarovat Contoso jako tenant, který se nastavení Tenanta omezení zásad, například dvojice název/hodnota vypadá takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Do své ID adresáře můžete najít [webu Azure portal](https://portal.azure.com). Přihlaste se jako správce, vyberte **Azure Active Directory**a pak vyberte **vlastnosti**.

Chcete-li zabránit uživatelům ve vkládání vlastní hlavičku HTTP s neschválené klienty, proxy musí nahradit hlavičku omezit přístup pro klienty, pokud je již k dispozici v příchozím požadavku. 

Klienti musí vynutit pro všechny požadavky login.microsoftonline.com login.microsoft.com a login.windows.net používal proxy server. Například pokud PAC soubory se používají k nasměrování klientů používal proxy server, koncoví uživatelé by neměl být schopni upravit nebo zakázat PAC soubory.

## <a name="the-user-experience"></a>Činnost koncového uživatele

Tato část popisuje prostředí pro koncové uživatele a správce.

### <a name="end-user-experience"></a>Činnost koncového uživatele

Příklad uživatel je v síti Contoso, ale se pokouší získat přístup k instanci Fabrikam sdílené SaaS aplikace jako je Outlook online. Contoso je v tenantovi není povolený pro tuto instanci, uživateli se zobrazí následující stránka:

![Přístup byl odepřen stránku pro uživatele v tenantech povoleno](./media/tenant-restrictions/end-user-denied.png)

### <a name="admin-experience"></a>Prostředí pro správu

Během konfigurace omezení klienta se provádí na infrastruktuře podnikový proxy server, můžou správci přímý přístup exportní Tenanta sestavy na webu Azure Portal. Chcete-li zobrazit sestavy, přejděte na stránku Přehled služby Azure Active Directory a podívejte se do části "Další možnosti".

ID správce pro tenanta, zadaný jako s omezeným přístupem. přístup kontextu tenanta můžete pomocí této sestavy naleznete v tématu přihlášení blokováno z důvodu zásad omezení Tenanta, včetně identity použité a cílový adresář. Přihlášení jsou zahrnuty, pokud nastavení tohoto omezení tenanta je tenantovi uživatele nebo prostředků tenanta pro přihlášení.

![Zobrazení s omezeným přístupem pokusů o přihlášení pomocí webu Azure portal](./media/tenant-restrictions/portal-report.png)

Stejně jako jiné sestavy na webu Azure Portal můžete použít filtry k zadání oboru sestavy. Můžete filtrovat na konkrétní uživatele, aplikace, klienta nebo časový interval.

## <a name="office-365-support"></a>Podpora Office 365

Aplikace Office 365, musí splňovat dvě kritéria pro úplnou podporu exportní Tenanta:

1. Podporuje klient používá moderní ověřování
2. Jako výchozí protokol ověřování pro cloudové služby je povolené moderní ověřování.

Odkazovat na [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) nejnovější informace, na které Office klientů aktuálně podporují moderní ověřování. Tuto stránku obsahuje také odkazy na pokyny pro povolení moderního ověřování v konkrétní Exchange Online a Skype pro firmy Online tenanty. Ve výchozím nastavení v Sharepointu Online je již povolené moderní ověřování.

Omezení tenanta v současné době podporuje aplikace využívající prohlížeč Office 365 (SharePoint portálu Office, Yammer, weby, Outlook na Web, atd.). Pro tlustých klientů (Outlook, Skype pro firmy, Word, Excel, PowerPoint atd.) Omezení tenanta může vynucovaly jenom při použití moderního ověřování.  

Outlook a Skype pro firmy klienty podporující moderní ověřování může stále moct používat starší protokoly s tenanty, kde není povolené moderní ověřování, efektivně obcházení omezení Tenanta. Aplikace, které používají starší protokoly mohou být blokovány exportní Tenanta, pokud kontaktovat login.microsoftonline.com login.microsoft.com a login.windows.net během ověřování.

Pro aplikaci Outlook na Windows mohou zákazníci zvolit implementovat omezení, zabrání koncovým uživatelům přidávání neschválený e-mailové účty do jejich profily. Viz například [zabránit přidání jiné než výchozí účty serveru Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) nastavení zásad skupiny.

## <a name="testing"></a>Testování

Pokud chcete vyzkoušet exportní Tenanta před implementací pro celou vaši organizaci, existují dvě možnosti: přístupu založené na hostiteli pomocí nástroje, jako jsou nástroje Fiddler nebo postupné zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pro přístup na základě hostitele

Fiddler je bezplatný webový ladicí proxy server, který slouží k zaznamenání a upravit přenosy HTTP/HTTPS, včetně vkládání hlavičky protokolu HTTP. Pokud chcete nakonfigurovat aplikace Fiddler k otestování exportní Tenanta, postupujte následovně:

1.  [Stáhnout a nainstalovat Fiddler](https://www.telerik.com/fiddler).
2.  Konfigurace aplikace Fiddler k dešifrování provozu HTTPS za [dokumentaci nápovědy v aplikaci Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).
3.  Konfigurace aplikace Fiddler k vložení *omezit přístup pro klienty* a *omezit přístup kontextu* záhlaví pomocí vlastního pravidla:
  1. V nástroji pro ladicí program webové aplikaci Fiddler, vyberte **pravidla** nabídky a vybereme **přizpůsobit pravidla...** Otevřít soubor CustomRules.
  2. Přidejte následující řádky na začátek *OnBeforeRequest* funkce. Nahraďte \<doména tenanta\> s doménou registrované v tenantu, například contoso.onmicrosoft.com. Nahraďte \<ID adresáře\> s identifikátorem Azure AD GUID vašeho tenanta.

  ```
  if (oSession.HostnameIs("login.microsoftonline.com") || oSession.HostnameIs("login.microsoft.com") || oSession.HostnameIs("login.windows.net")){      oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";      oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";}
  ```

  Pokud je potřeba povolit více tenantů, oddělte názvy tenanta pomocí čárku. Příklad:

  ```
  oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";
  ```

4. Uložte a zavřete soubor CustomRules.

Po dokončení konfigurace Fiddler, můžete zaznamenávat provoz tak, že přejdete **souboru** nabídky a vyberete **zachycování provozu**.

### <a name="staged-rollout-of-proxy-settings"></a>Postupné zavedení nastavení proxy serveru

V závislosti na možnosti vaší infrastruktury služby proxy budete moci fáze zavedení nastavení pro vaše uživatele. Tady jsou několik možností vysoké úrovně pro zvážení:

1.  Použijte PAC soubory pro testovací uživatele přejděte testovací infrastrukturu proxy server při normálním uživatelé nadále používat provozní infrastruktuře proxy.
2.  Některé proxy servery můžou podporovat různé konfigurace pomocí skupin.

V dokumentaci proxy server pro konkrétní podrobnosti.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/)

- Zkontrolujte [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
