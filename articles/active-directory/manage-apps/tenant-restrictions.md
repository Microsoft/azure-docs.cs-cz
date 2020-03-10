---
title: Použití omezení tenanta ke správě přístupu k SaaS aplikacím – Azure AD
description: Jak používat omezení tenanta ke správě uživatelů, kteří budou mít přístup k aplikacím na základě jejich tenanta Azure AD.
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
ms.openlocfilehash: 70cdb4b42e835a9bfa03f4551ba25088ef8c5226
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942857"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Použití omezení tenanta ke správě přístupu k SaaS cloudovým aplikacím

Velké organizace, které zdůrazňují zabezpečení chcete přesunout do cloudové služby, jako je Office 365, ale třeba vědět, že jejich pouze mohou uživatelé schválené prostředky. Společnosti tradičně, omezení názvů domény nebo IP adres, ji můžete spravovat přístup. Tento přístup se nezdařil v celém světě, kde jsou aplikace typu software jako služba (nebo SaaS) hostované ve veřejném cloudu a běží na sdílených doménách, jako je [Outlook.Office.com](https://outlook.office.com/) a [Login.microsoftonline.com](https://login.microsoftonline.com/). Blokování tyto adresy byste zabránit uživatelům v přístupu k aplikaci Outlook na webu úplně, namísto pouze omezení schválené identity a prostředky.

Řešení Azure Active Directory (Azure AD) pro tuto výzvu je funkce s názvem omezení tenanta. Díky omezením tenanta můžou organizace řídit přístup k SaaS cloudovým aplikacím, a to na základě tenanta Azure AD, které aplikace používají pro jednotné přihlašování. Chcete například povolit přístup k aplikacím Office 365 vaší organizace, zároveň je možné zabránit přístupu do jiných organizací instancí tyto stejné aplikace.  

V případě omezení tenanta můžou organizace určovat seznam tenantů, ke kterým mají uživatelé přístup. Azure AD pak pouze uděluje přístup k těmto klientům povoleno.

Tento článek se zaměřuje na omezení klientů pro Office 365, ale tato funkce by měla fungovat s libovolnou cloudovou aplikací SaaS, která používá moderní ověřovací protokoly se službou Azure AD pro jednotné přihlašování. Pokud používáte SaaS aplikací pomocí jiné služby Azure AD tenanta z tenanta používaný v Office 365, ujistěte se, že všechny požadované jsou klientům povolené. Další informace o cloudových aplikacích SaaS najdete na [webu Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Celkové řešení se skládá z následujících součástí:

1. **Azure AD**: pokud je `Restrict-Access-To-Tenants: <permitted tenant list>` k dispozici, Azure AD vydává tokeny zabezpečení jenom pro povolené klienty.

2. **Místní infrastruktura proxy server**: Tato infrastruktura je proxy zařízení, které umožňuje kontrolu SSL (Secure SOCKETS Layer) (SSL). Musíte nakonfigurovat proxy server, aby se vložila hlavička obsahující seznam povolených klientů do provozu určeného pro Azure AD.

3. **Klientský software**: aby bylo možné podporovat omezení klientů, musí klientský software požádat o tokeny přímo z Azure AD, aby mohla infrastruktura proxy zachytit provoz. Aplikace Office 365 založené na prohlížeči aktuálně podporují omezení tenanta, stejně jako klienti Office, kteří používají moderní ověřování (například OAuth 2,0).

4. **Moderní ověřování**: cloudové služby musí používat moderní ověřování k používání omezení tenanta a zablokovat přístup ke všem nepovoleným klientům. Ve výchozím nastavení je nutné nakonfigurovat cloudové služby Office 365 pro použití moderních ověřovacích protokolů. Nejnovější informace o podpoře pro moderní ověřování v Office 365 najdete v tématu [aktualizované moderní ověřování pro office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Následující diagram znázorňuje tok provozu vysoké úrovně. Omezení tenanta vyžadují kontrolu SSL jenom v provozu do Azure AD, nikoli cloudové služby Office 365. Toto rozlišení je důležité, protože objem provozu pro ověřování do služby Azure AD je obvykle mnohem menší než objem přenosu, aby SaaS aplikace jako Exchange Online a SharePoint Online.

![Tok přenosů omezení tenanta – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení tenanta

Existují dva kroky, jak začít s omezeními tenanta. Nejprve se ujistěte, že se klienti mohou připojit ke správným adresám. Potom nakonfigurujte infrastrukturu proxy serveru.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Aby bylo možné použít omezení tenanta, klienti musí být schopni se připojit k následujícím adresám URL služby Azure AD a ověřit: [Login.microsoftonline.com](https://login.microsoftonline.com/), [Login.Microsoft.com](https://login.microsoft.com/)a [Login.Windows.NET](https://login.windows.net/). Pro přístup k sadě Office 365 musí být navíc klienti schopni se připojit k plně kvalifikovaným názvům domén (FQDN), adresám URL a IP adresám definovaným v [adresách URL sady Office 365 a v rozsahu IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace proxy serveru a požadavky

K povolení omezení klientů prostřednictvím vaší proxy infrastruktury se vyžaduje následující konfigurace. Tyto pokyny je obecný, takže by měla odkazovat na dokumentaci od dodavatele proxy server pro konkrétní implementaci.

#### <a name="prerequisites"></a>Předpoklady

- Proxy server musí být schopen provést SSL zachycení, HTTP záhlaví vložení a filtrovat pomocí plně kvalifikovaných názvů domén a adres URL cíle.

- Klienti musí důvěřovat řetězu certifikátů, které jsou prezentované podle proxy serveru k zajištění komunikace SSL. Pokud se například použijí certifikáty z interní [infrastruktury veřejných klíčů (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) , musí být interní vydávající certifikát kořenové certifikační autority důvěryhodný.

- Tato funkce je součástí předplatných sady Office 365, ale pokud chcete k řízení přístupu k ostatním aplikacím SaaS použít omezení tenanta, je nutné licence Azure AD Premium 1.

#### <a name="configuration"></a>Konfigurace

Pro každý příchozí požadavek do login.microsoftonline.com, login.microsoft.com a login.windows.net vložte dvě hlavičky HTTP: *omezení přístupu k tenantovi* a *omezení přístupu-Access-Context*.

Záhlaví by měl obsahovat následující prvky:

- V případě *omezení přístupu k tenantovi*použijte hodnotu \<povoleného seznamu klientů\>, což je seznam klientů oddělených čárkami, kterým chcete povolit přístup uživatelům. Do libovolné domény, který je registrovaný pomocí tenanta můžete použít k identifikaci tenanta služby v tomto seznamu. Chcete-li například povolit přístup k klientům společnosti Contoso i Fabrikam, bude dvojice název/hodnota vypadat takto: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Pro *kontext omezení přístupu*použijte hodnotu jednoho ID adresáře, která deklaruje, který tenant nastavuje omezení tenanta. Chcete-li například deklarovat contoso jako tenanta, který nastaví zásady omezení klientů, bude dvojice název/hodnota vypadat takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> ID vašeho adresáře najdete na [portálu Azure Active Directory](https://aad.portal.azure.com/). Přihlaste se jako správce, vyberte **Azure Active Directory**a pak vyberte **vlastnosti**.

Aby uživatelé nemohli vkládat vlastní hlavičku HTTP s neschválenými klienty, musí proxy nahradit hlavičku *omezení-Access-to-tenant* , pokud už je v příchozím požadavku přítomná.

Klienti musí vynutit pro všechny požadavky login.microsoftonline.com login.microsoft.com a login.windows.net používal proxy server. Například pokud jsou soubory PAC použity k přímému směrování klientů na používání proxy serveru, neměli by koncoví uživatelé moci upravovat ani zakazovat soubory PAC.

## <a name="the-user-experience"></a>Činnost koncového uživatele

Tato část popisuje prostředí pro koncové uživatele i správce.

### <a name="end-user-experience"></a>Činnost koncového uživatele

Příklad uživatel je v síti Contoso, ale se pokouší získat přístup k instanci Fabrikam sdílené SaaS aplikace jako je Outlook online. Pokud je Fabrikam neautorizovaným klientem pro instanci contoso, uživateli se zobrazí zpráva o odepření přístupu, která říká, že se snažíte získat přístup k prostředku, který patří do organizace, kterou vaše IT oddělení neschválilo.

### <a name="admin-experience"></a>Činnost správce

I když se konfigurace omezení tenanta provádí v podnikové infrastruktuře proxy serveru, můžou správci získat přístup k sestavám omezení klientů přímo v Azure Portal. Zobrazení sestav:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se řídicí panel **centra pro správu Azure Active Directory** .

2. V levém podokně vyberte **Azure Active Directory**. Zobrazí se stránka přehled Azure Active Directory.

3. V části **Další možnosti** vyberte **omezení tenanta**.

Správce pro tenanta, který je zadaný jako tenant s omezeným přístupem, může pomocí této sestavy zobrazit blokované přihlášení kvůli zásadám omezení tenanta, včetně použité identity a ID cílového adresáře. Přihlášení jsou zahrnuty, pokud nastavení tohoto omezení tenanta je tenantovi uživatele nebo prostředků tenanta pro přihlášení.

> [!NOTE]
> Sestava může obsahovat omezené informace, jako je ID cílového adresáře, když se uživatel, který je v jiném klientovi než se přihlásí jako tenant s omezeným přístupem. V takovém případě jsou uživatelsky identifikovatelné informace, jako je název a hlavní název uživatele, maskovány pro ochranu uživatelských dat v jiných klientech.

Stejně jako jiné sestavy na webu Azure Portal můžete použít filtry k zadání oboru sestavy. Můžete filtrovat podle určitého časového intervalu, uživatele, aplikace, klienta nebo stavu. Pokud vyberete tlačítko **sloupce** , můžete se rozhodnout zobrazit data s libovolnou kombinací následujících polí:

- **Uživatelský**
- **Použití**
- **Stav**
- **Datum**
- **Datum (UTC)** (kde UTC je koordinovaný světový čas)
- **Metoda ověřování MFA** (metoda vícefaktorového ověřování)
- **Podrobnosti** o ověřování MFA (podrobnosti o vícefaktorového ověřování)
- **Výsledek MFA**
- **IP adresa**
- **Služba**
- **Uživatelské jméno**
- **Umístění**
- **ID cílového tenanta**

## <a name="office-365-support"></a>Podpora Office 365

Aby bylo možné plně podporovat omezení klientů, aplikace Office 365 musí splňovat dvě kritéria:

1. Použitý klient podporuje moderní ověřování.
2. Jako výchozí protokol ověřování pro cloudové služby je povolené moderní ověřování.

Nejnovější informace o tom, které klienty Office aktuálně podporují moderní ověřování, najdete v tématu [aktualizované moderní ověřování Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) . Tuto stránku obsahuje také odkazy na pokyny pro povolení moderního ověřování v konkrétní Exchange Online a Skype pro firmy Online tenanty. SharePoint Online už ve výchozím nastavení umožňuje moderní ověřování.

Aplikace založené na prohlížeči Office 365 (portál Office, Yammer, weby SharePointu, Outlook na webu a další) aktuálně podporují omezení klientů. Silné klienty (Outlook, Skype pro firmy, Word, Excel, PowerPoint a další) můžou vymáhat omezení tenanta jenom v případě, že se používá moderní ověřování.  

Klienti Outlooku a Skype pro firmy, kteří podporují moderní ověřování, můžou pořád používat starší protokoly pro klienty, u kterých není povolené moderní ověřování, a efektivně tak obejít omezení tenanta. Omezení tenanta můžou blokovat aplikace, které používají starší protokoly, pokud při ověřování kontaktují login.microsoftonline.com, login.microsoft.com nebo login.windows.net.

Pro aplikaci Outlook na Windows mohou zákazníci zvolit implementovat omezení, zabrání koncovým uživatelům přidávání neschválený e-mailové účty do jejich profily. Přečtěte si například nastavení zásad skupiny [Zakázat přidávání jiných než výchozích účtů Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testování

Pokud si chcete vyzkoušet omezení tenanta před implementací pro celou organizaci, máte dvě možnosti: přístup založený na hostiteli pomocí nástroje, jako je Fiddler, nebo dvoufázové zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pro přístup na základě hostitele

Fiddler je bezplatný webový ladicí proxy server, který slouží k zaznamenání a upravit přenosy HTTP/HTTPS, včetně vkládání hlavičky protokolu HTTP. Pokud chcete nakonfigurovat Fiddler pro testování omezení tenanta, proveďte následující kroky:

1. [Stáhněte a nainstalujte Fiddler](https://www.telerik.com/fiddler).

2. Nakonfigurujte Fiddler pro dešifrování provozu HTTPS, [dokumentace k nápovědě pro Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Nakonfigurovat Fiddler pro vložení hlaviček *omezení přístupu k tenantovi* a *omezení přístupu kontextu* pomocí vlastních pravidel:

   1. V nástroji Fiddler webového ladicího programu vyberte nabídku **pravidla** a vyberte **přizpůsobit pravidla...** Otevřít soubor CustomRules.

   2. Na začátek funkce `OnBeforeRequest` přidejte následující řádky. \> doménu tenanta \<nahraďte doménou registrovanou pro vašeho tenanta (například `contoso.onmicrosoft.com`). Nahraďte ID \<adresáře\> identifikátorem GUID Azure AD vašeho tenanta.

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

Po nakonfigurování Fiddler můžete zachytit provoz tak, že kliknete na nabídku **soubor** a vyberete možnost **zachytit přenos**.

### <a name="staged-rollout-of-proxy-settings"></a>Postupné zavedení nastavení proxy serveru

V závislosti na možnosti vaší infrastruktury služby proxy budete moci fáze zavedení nastavení pro vaše uživatele. Tady jsou několik možností vysoké úrovně pro zvážení:

1. Použijte PAC soubory pro testovací uživatele přejděte testovací infrastrukturu proxy server při normálním uživatelé nadále používat provozní infrastruktuře proxy.
2. Některé proxy servery můžou podporovat různé konfigurace pomocí skupin.

Konkrétní podrobnosti najdete v dokumentaci k proxy server.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [aktualizovaném moderním ověřování pro Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) .
- Kontrola [adres URL a rozsahů IP adres Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
