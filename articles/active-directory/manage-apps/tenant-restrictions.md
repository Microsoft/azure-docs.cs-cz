---
title: Omezení tenanta můžete spravovat přístup k SaaS cloudovým aplikacím – Azure | Dokumentace Microsoftu
description: Jak používat exportní tenanta pro správu, které uživatelé můžou používat aplikace na základě jejich tenantů Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824382"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Umožňuje spravovat přístup ke cloudovým aplikacím SaaS exportní tenanta

Velké organizace, které zdůrazňují zabezpečení chcete přesunout do cloudové služby, jako je Office 365, ale třeba vědět, že jejich pouze mohou uživatelé schválené prostředky. Společnosti tradičně, omezení názvů domény nebo IP adres, ji můžete spravovat přístup. Tento postup selže ve světě hostuje software jako služba (nebo platformám SaaS) aplikací ve veřejném cloudu, které běží na názvy sdílená doména jako [outlook.office.com](https://outlook.office.com/) a [login.microsoftonline.com](https://login.microsoftonline.com/). Blokování tyto adresy byste zabránit uživatelům v přístupu k aplikaci Outlook na webu úplně, namísto pouze omezení schválené identity a prostředky.

Řešení Azure Active Directory (Azure AD), aby tento problém je funkce volána exportní tenanta. Omezení tenanta organizace můžete řídit přístup ke cloudovým aplikacím SaaS, založené na tenanta Azure AD, které aplikace se používají pro jednotné přihlašování. Chcete například povolit přístup k aplikacím Office 365 vaší organizace, zároveň je možné zabránit přístupu do jiných organizací instancí tyto stejné aplikace.  

Omezení tenanta organizace mohou určit seznam klientů, které mají povolen přístup k svým uživatelům. Azure AD pak pouze uděluje přístup k těmto klientům povoleno.

Tento článek se zaměřuje na klienta omezení pro Office 365, ale tato funkce by měla fungovat s žádnou cloudovou aplikaci SaaS, která používá moderní ověřování protokoly s Azure AD pro jednotné přihlašování. Pokud používáte SaaS aplikací pomocí jiné služby Azure AD tenanta z tenanta používaný v Office 365, ujistěte se, že všechny požadované jsou klientům povolené. Další informace o cloudových aplikacích SaaS, najdete v článku [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Celkové řešení se skládá z následujících součástí:

1. **Azure AD**: Pokud `Restrict-Access-To-Tenants: <permitted tenant list>` je k dispozici, Azure AD pouze problémy s tokeny zabezpečení pro povolených tenantů.

2. **Místní proxy server infrastruktury**: Tato infrastruktura se proxy zařízení schopné kontroly vrstvy SSL (Secure Sockets). Je nutné nakonfigurovat proxy server pro vložení záhlaví obsahující seznam povolených tenantů do provozu určeného pro službu Azure AD.

3. **Klientský software**: Podporovat exportní tenanta, klientský software musí požádat o tokeny přímo ze služby Azure AD tak, aby infrastruktury proxy může zachytávat provoz. Aplikace využívající prohlížeč Office 365 aktuálně podporují exportní tenanta, stejně jako klienti Office, které používají moderní ověřování (jako jsou OAuth 2.0).

4. **Moderní ověřování**: Cloudové služby musí používat omezení klienta a zablokovat přístup pro všechny tenanty bez povolené používají moderní ověřování. Je nutné nakonfigurovat cloudové služby Office 365, které používají moderní ověřování protokoly ve výchozím nastavení. Nejnovější informace o podpoře služeb Office 365 pro moderní ověřování, přečtěte si [moderní ověřování Office 365 aktualizovat](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Následující diagram znázorňuje tok provozu vysoké úrovně. Omezení tenanta nevyžadují kontrolu SSL pouze na provoz do služby Azure AD, nikoli do cloudové služby Office 365. Tento rozdíl je důležitý, protože objem přenosů pro ověření do služby Azure AD je obvykle mnohem nižší než objem provozu pro aplikace SaaS, jako je Exchange Online a SharePoint Online.

![Tenant omezení toku provozu – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení tenanta

Existují dva kroky, abyste mohli začít s omezeními pro tenanta. Nejprve se ujistěte, jestli vaši klienti může připojit ke správné adresy. Za druhé konfigurace vaší infrastruktury služby proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Pokud chcete použít exportní tenanta, musí být schopný se připojit k následujícím adresám URL Azure AD k ověření vaši klienti: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), a [ Login.Windows.NET](https://login.windows.net/). Kromě toho pro přístup k Office 365, klienti musí být také moci připojit k plně kvalifikované názvy domény (FQDN) adresy URL a IP adresy definované v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace proxy serveru a požadavky

Aktivaci tenanta omezení prostřednictvím vaší infrastruktury služby proxy je nutná tato konfigurace. Tyto pokyny je obecný, takže by měla odkazovat na dokumentaci od dodavatele proxy server pro konkrétní implementaci.

#### <a name="prerequisites"></a>Požadavky

- Proxy server musí být schopen provést SSL zachycení, HTTP záhlaví vložení a filtrovat pomocí plně kvalifikovaných názvů domén a adres URL cíle.

- Klienti musí důvěřovat řetězu certifikátů, které jsou prezentované podle proxy serveru k zajištění komunikace SSL. Například pokud certifikáty z interní [infrastruktury veřejných klíčů (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) se používá, interní vydávající certifikát kořenové certifikační autority musí být důvěryhodný.

- Tato funkce je zahrnutá v předplatných Office 365, ale pokud chcete používat tenanta omezení řízení přístupu k jiným aplikacím SaaS, pak licence Azure AD Premium 1 se vyžadují.

#### <a name="configuration"></a>Konfigurace

Pro každého příchozího požadavku login.microsoftonline.com login.microsoft.com a login.windows.net, vložte dva hlavičky protokolu HTTP: *Omezit přístup na Tenantů* a *omezit kontextu přístup*.

Záhlaví by měl obsahovat následující prvky:

- Pro *omezit přístup pro klienty*, použijte hodnotu \<povolené seznamu v tenantovi\>, což je čárkou oddělený seznam klientů, které chcete povolit uživatelům přístup k. Do libovolné domény, který je registrovaný pomocí tenanta můžete použít k identifikaci tenanta služby v tomto seznamu. Například tak, aby povolovala přístup pro klienty Contoso a Fabrikam, dvojice název/hodnota vypadá takto: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Pro *omezit přístup kontextu*, použijte hodnotu ID jeden adresář, kterého tenanta deklarace je nastavení tenanta omezení. Například chcete-li deklarovat Contoso jako tenant, který nastavení zásady omezení tenanta, dvojice název/hodnota vypadá takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Do své ID adresáře můžete najít [portálu Azure Active Directory](https://aad.portal.azure.com/). Přihlaste se jako správce, vyberte **Azure Active Directory**a pak vyberte **vlastnosti**.

Chcete-li zabránit uživatelům ve vkládání vlastní hlavičku HTTP s neschválené klienty, proxy server potřebuje nahradit *omezit přístup pro klienty* záhlaví, pokud je již k dispozici v příchozím požadavku.

Klienti musí vynutit pro všechny požadavky login.microsoftonline.com login.microsoft.com a login.windows.net používal proxy server. Například pokud PAC soubory se používají k nasměrování klientů používal proxy server, koncoví uživatelé by neměl být schopni upravit nebo zakázat PAC soubory.

## <a name="the-user-experience"></a>Činnost koncového uživatele

Tato část popisuje prostředí pro koncové uživatele a správce.

### <a name="end-user-experience"></a>Činnost koncového uživatele

Příklad uživatel je v síti Contoso, ale se pokouší získat přístup k instanci Fabrikam sdílené SaaS aplikace jako je Outlook online. Pokud společnost Fabrikam je tenant není povolené pro instanci Contoso, uživateli se zobrazí zpráva o odepření přístupu, která říká, že se pokoušíte přistupovat k prostředku, který patří do organizace neschválených vaším IT oddělením.

### <a name="admin-experience"></a>Prostředí pro správu

Během konfigurace omezení klienta se provádí na infrastruktuře podnikový proxy server, můžou správci přímý přístup sestavy omezení tenanta na portálu Azure portal. Chcete-li zobrazit sestavy:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). **Centra pro správu Azure Active Directory** se zobrazí řídicí panel.

2. V levém podokně vyberte **Azure Active Directory**. Zobrazí se stránka s přehledem Azure Active Directory.

3. V **další možnosti** záhlaví, vyberte **Tenanta omezení**.

ID správce pro tenanta, zadaný jako s omezeným přístupem. přístup kontextu tenanta můžete pomocí této sestavy naleznete v tématu přihlášení blokováno z důvodu omezení zásad klienta, včetně identity použité a cílový adresář. Přihlášení jsou zahrnuty, pokud nastavení tohoto omezení tenanta je tenantovi uživatele nebo prostředků tenanta pro přihlášení.

Stejně jako jiné sestavy na webu Azure Portal můžete použít filtry k zadání oboru sestavy. Můžete filtrovat podle určitého časového intervalu, uživatele, aplikace, klienta nebo stav. Pokud vyberete **sloupce** tlačítko, můžete k zobrazení dat s libovolnou kombinací následující pole:

- **Uživatel**
- **Aplikace**
- **Stav**
- **Datum**
- **Datum (UTC)** (kde UTC je koordinovaný univerzální čas)
- **Ověřovací metoda MFA** (vícefaktorové ověřování)
- **Podrobnosti ověřování MFA** (podrobnosti o vícefaktorové ověřování)
- **Výsledek vícefaktorového ověřování**
- **IP adresa**
- **Klienta**
- **Uživatelské jméno**
- **Umístění**
- **ID cílového tenanta**

## <a name="office-365-support"></a>Podpora Office 365

Aplikace Office 365, musí splňovat dvě kritéria pro úplnou podporu exportní tenanta:

1. Podporuje klient používá moderní ověřování.
2. Jako výchozí protokol ověřování pro cloudové služby je povolené moderní ověřování.

Odkazovat na [moderní ověřování Office 365 aktualizovat](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) nejnovější informace, na které Office klientů aktuálně podporují moderní ověřování. Tuto stránku obsahuje také odkazy na pokyny pro povolení moderního ověřování v konkrétní Exchange Online a Skype pro firmy Online tenanty. SharePoint Online už moderní ověřování ve výchozím nastavení povoluje.

Aplikace založené na prohlížeči Office 365 (SharePoint portálu Office, Yammer, weby, Outlook na webu a další) v současné době podporují exportní tenanta. Tlustých klientů (Outlook, Skype pro firmy, Word, Excel, PowerPoint a více) můžete vynutit omezení tenanta jenom při použití moderního ověřování.  

Outlook a Skype pro firmy klienty podporující moderní ověřování může stále moct používat starší protokoly s tenanty, kde není povolené moderní ověřování, efektivně obcházení omezení tenanta. Omezení tenanta může blokovat aplikace, které používají starší protokoly, pokud kontaktovat login.microsoftonline.com login.microsoft.com a login.windows.net během ověřování.

Pro aplikaci Outlook na Windows mohou zákazníci zvolit implementovat omezení, zabrání koncovým uživatelům přidávání neschválený e-mailové účty do jejich profily. Viz například [zabránit přidání jiné než výchozí účty serveru Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) nastavení zásad skupiny.

## <a name="testing"></a>Testování

Pokud chcete vyzkoušet exportní tenanta před implementací pro celou vaši organizaci, máte dvě možnosti: přístupu založené na hostiteli pomocí nástroje, jako jsou nástroje Fiddler nebo postupné zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pro přístup na základě hostitele

Fiddler je bezplatný webový ladicí proxy server, který slouží k zaznamenání a upravit přenosy HTTP/HTTPS, včetně vkládání hlavičky protokolu HTTP. Pokud chcete nakonfigurovat aplikace Fiddler k otestování exportní tenanta, postupujte následovně:

1. [Stáhnout a nainstalovat Fiddler](https://www.telerik.com/fiddler).

2. Konfigurace aplikace Fiddler k dešifrování provozu HTTPS za [dokumentaci nápovědy v aplikaci Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurace aplikace Fiddler k vložení *omezit přístup pro klienty* a *omezit přístup kontextu* záhlaví pomocí vlastního pravidla:

   1. V nástroji pro ladicí program webové aplikaci Fiddler, vyberte **pravidla** nabídky a vybereme **přizpůsobit pravidla...** Otevřít soubor CustomRules.

   2. Přidejte následující řádky na začátek `OnBeforeRequest` funkce. Nahraďte \<doména tenanta\> s doménou zaregistrována ve vašem tenantovi (například `contoso.onmicrosoft.com`). Nahraďte \<ID adresáře\> s identifikátorem Azure AD GUID vašeho tenanta.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Pokud je potřeba povolit více tenantů, oddělte názvy tenanta pomocí čárku. Příklad:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Uložte a zavřete soubor CustomRules.

Po dokončení konfigurace Fiddler, můžete zaznamenávat provoz tak, že přejdete **souboru** nabídky a vyberete **zachycování provozu**.

### <a name="staged-rollout-of-proxy-settings"></a>Postupné zavedení nastavení proxy serveru

V závislosti na možnosti vaší infrastruktury služby proxy budete moci fáze zavedení nastavení pro vaše uživatele. Tady jsou několik možností vysoké úrovně pro zvážení:

1. Použijte PAC soubory pro testovací uživatele přejděte testovací infrastrukturu proxy server při normálním uživatelé nadále používat provozní infrastruktuře proxy.
2. Některé proxy servery můžou podporovat různé konfigurace pomocí skupin.

Konkrétní podrobnosti najdete v dokumentaci k proxy serveru.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [moderní ověřování Office 365 aktualizovat](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Zkontrolujte [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
