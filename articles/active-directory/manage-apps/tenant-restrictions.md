---
title: Použití omezení tenanta ke správě přístupu k SaaS aplikacím – Azure AD
description: Jak používat omezení tenanta ke správě uživatelů, kteří budou mít přístup k aplikacím na základě jejich tenanta Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 2/23/2021
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 611dd5e53ae96e06677b1c4a6a6f009e582b33af
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646261"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Použití omezení tenanta ke správě přístupu k SaaS cloudovým aplikacím

Velké organizace, které zdůrazňují zabezpečení chcete přejít ke cloudovým službám, jako je Microsoft 365, ale potřebují, aby měli jistotu, že uživatelé mají přístup jenom ke schváleným prostředkům. Obvykle společnosti omezují názvy domén nebo IP adresy, když chtějí spravovat přístup. Tento přístup se nezdařil v celém světě, kde jsou aplikace typu software jako služba (nebo SaaS) hostované ve veřejném cloudu a běží na sdílených doménách, jako je [Outlook.Office.com](https://outlook.office.com/) a [Login.microsoftonline.com](https://login.microsoftonline.com/). Blokováním těchto adres budou uživatelé mít naprosto přístup k aplikaci Outlook na webu, místo aby je neomezili pouze na schválené identity a prostředky.

Řešení Azure Active Directory (Azure AD) pro tuto výzvu je funkce s názvem omezení tenanta. Díky omezením tenanta můžou organizace řídit přístup k SaaS cloudovým aplikacím, a to na základě tenanta Azure AD, které aplikace používají pro jednotné přihlašování. Můžete například chtít dovolit přístup k aplikacím Microsoft 365 vaší organizace a zároveň zabránit přístupu k instancím jiných organizací v těchto aplikacích.  

V případě omezení tenanta můžou organizace určovat seznam tenantů, ke kterým mají uživatelé přístup. Azure AD pak udělí přístup jenom těmto povoleným klientům.

Tento článek se zaměřuje na omezení tenanta pro Microsoft 365, ale funkce chrání všechny aplikace, které odesílají uživatele do služby Azure AD pro jednotné přihlašování. Pokud používáte aplikace SaaS s jiným klientem Azure AD z tenanta používaného vaší Microsoft 365, ujistěte se, že jsou všichni povinní klienti povoleni (například ve scénářích spolupráce B2B). Další informace o cloudových aplikacích SaaS najdete na [webu Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

Kromě toho funkce omezení tenanta teď podporuje [blokování použití všech zákaznických aplikací Microsoftu](#blocking-consumer-applications) (MSA aplikací), jako jsou OneDrive, Hotmail a Xbox.com.  K tomuto `login.live.com` koncovému bodu se používá samostatná hlavička a na konci dokumentu se podrobně podrobná.

## <a name="how-it-works"></a>Jak to funguje

Celkové řešení zahrnuje tyto komponenty:

1. **Azure AD**: Pokud `Restrict-Access-To-Tenants: <permitted tenant list>` je hlavička k dispozici, Azure AD vydává tokeny zabezpečení jenom pro povolené klienty.

2. **Místní infrastruktura proxy server**: Tato infrastruktura je proxy zařízení podporující kontrolu TLS (Transport Layer Security). Musíte nakonfigurovat proxy server, aby se vložila hlavička obsahující seznam povolených klientů do provozu určeného pro Azure AD.

3. **Klientský software**: aby bylo možné podporovat omezení klientů, musí klientský software požádat o tokeny přímo z Azure AD, aby mohla infrastruktura proxy zachytit provoz. Microsoft 365 aplikace založené na prohlížeči aktuálně podporují omezení tenanta, stejně jako klienti Office, kteří používají moderní ověřování (například OAuth 2,0).

4. **Moderní ověřování**: cloudové služby musí používat moderní ověřování k používání omezení tenanta a zablokovat přístup ke všem nepovoleným klientům. Ve výchozím nastavení musíte nakonfigurovat Microsoft 365 cloudové služby pro použití moderních ověřovacích protokolů. Nejnovější informace o podpoře moderního ověřování v Microsoft 365 najdete v tématu [aktualizované moderní ověřování pro Office 365](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Následující diagram znázorňuje tok provozu na vysoké úrovni. Omezení klientů vyžadují kontrolu TLS pouze při provozu do služby Azure AD, nikoli do cloudové služby Microsoft 365. Toto rozlišení je důležité, protože objem provozu pro ověřování do služby Azure AD je obvykle mnohem menší než objem přenosu, aby SaaS aplikace jako Exchange Online a SharePoint Online.

![Tok přenosů omezení tenanta – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení tenanta

Existují dva kroky, jak začít s omezeními tenanta. Nejprve se ujistěte, že se klienti mohou připojit ke správným adresám. Potom nakonfigurujte infrastrukturu proxy serveru.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Aby bylo možné použít omezení tenanta, klienti musí být schopni se připojit k následujícím adresám URL služby Azure AD a ověřit: [Login.microsoftonline.com](https://login.microsoftonline.com/), [Login.Microsoft.com](https://login.microsoft.com/)a [Login.Windows.NET](https://login.windows.net/). Pro přístup k sadě Office 365 musí být navíc klienti schopni se připojit k plně kvalifikovaným názvům domén (FQDN), adresám URL a IP adresám definovaným v [adresách URL sady Office 365 a v rozsahu IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace a požadavky proxy serveru

K povolení omezení klientů prostřednictvím vaší proxy infrastruktury se vyžaduje následující konfigurace. Tyto doprovodné materiály jsou obecné, takže byste měli v dokumentaci dodavatele proxy serveru vyhledat konkrétní kroky implementace.

#### <a name="prerequisites"></a>Požadavky

- Proxy musí být schopné provádět zachycení protokolu TLS, vkládání hlaviček HTTP a filtrovat cíle pomocí plně kvalifikovaného názvu domény nebo adresy URL.

- Klienti musí důvěřovat řetězu certifikátů, který prezentuje proxy server pro komunikaci TLS. Pokud se například použijí certifikáty z interní [infrastruktury veřejných klíčů (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) , musí být interní vydávající certifikát kořenové certifikační autority důvěryhodný.

- Pro použití omezení tenanta se vyžadují licence Azure AD Premium 1.

#### <a name="configuration"></a>Konfigurace

Pro každý odchozí požadavek na login.microsoftonline.com, login.microsoft.com a login.windows.net vložte dvě hlavičky HTTP: *omezení přístupu k tenantovi* a *omezení přístupu-Access-Context*.

> [!NOTE]
> `*.login.microsoftonline.com`Do konfigurace proxy serveru nezahrnujte subdomény. To bude zahrnovat device.login.microsoftonline.com a bude v konfliktu s ověřováním klientských certifikátů, který se používá při registraci zařízení a ve scénářích podmíněného přístupu na základě zařízení. Nakonfigurujte proxy server tak, aby vyloučil device.login.microsoftonline.com z přerušení a kontroly pomocí protokolu TLS a vkládání hlaviček.

Hlavičky by měly obsahovat následující prvky:

- V případě *omezení přístupu k tenantovi* použijte hodnotu \<permitted tenant list\> , která je čárkami oddělený seznam klientů, kterým chcete povolit přístup uživatelům. Všechny domény, které jsou zaregistrované v tenantovi, se dají použít k identifikaci tenanta v tomto seznamu a také samotného ID adresáře. Příklad všech tří způsobů, jak popsat tenanta, dvojice název/hodnota, která umožní společnostem contoso, Fabrikam a Microsoft, vypadá takto: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Pro *kontext omezení přístupu* použijte hodnotu jednoho ID adresáře, která deklaruje, který tenant nastavuje omezení tenanta. Chcete-li například deklarovat contoso jako klienta, který nastaví zásady omezení klientů, bude dvojice název/hodnota vypadat takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Aby bylo možné získat protokoly pro tato ověření, **musíte** v tomto místě použít vlastní ID adresáře.

> [!TIP]
> ID vašeho adresáře najdete na [portálu Azure Active Directory](https://aad.portal.azure.com/). Přihlaste se jako správce, vyberte **Azure Active Directory** a pak vyberte **vlastnosti**. 
>
> Pokud chcete ověřit, že ID adresáře nebo název domény odkazují na stejného tenanta, použijte toto ID nebo doménu místo <tenant> v této adrese URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Pokud jsou výsledky s doménou a ID stejné, odkazují na stejného tenanta. 

Aby uživatelé nemohli vkládat vlastní hlavičku HTTP s neschválenými klienty, musí proxy nahradit hlavičku *omezení-Access-to-tenant* , pokud už je v příchozím požadavku přítomná.

Klienti musí mít nuceně používat proxy server pro všechny požadavky na login.microsoftonline.com, login.microsoft.com a login.windows.net. Například pokud jsou soubory PAC použity k přímému směrování klientů na používání proxy serveru, neměli by koncoví uživatelé moci upravovat ani zakazovat soubory PAC.

## <a name="the-user-experience"></a>Činnost koncového uživatele

Tato část popisuje prostředí pro koncové uživatele i správce.

### <a name="end-user-experience"></a>Prostředí koncového uživatele

Ukázkový uživatel se nachází v síti Contoso, ale pokouší se získat přístup k instanci Fabrikam sdílené aplikace SaaS, jako je Outlook online. Pokud je Fabrikam neautorizovaným klientem pro instanci contoso, uživateli se zobrazí zpráva o odepření přístupu, která říká, že se snažíte získat přístup k prostředku, který patří do organizace, kterou vaše IT oddělení neschválilo.

### <a name="admin-experience"></a>Prostředí pro správu

I když se konfigurace omezení tenanta provádí v podnikové infrastruktuře proxy serveru, můžou správci získat přístup k sestavám omezení klientů přímo v Azure Portal. Zobrazení sestav:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se řídicí panel **centra pro správu Azure Active Directory** .

2. V levém podokně vyberte **Azure Active Directory**. Zobrazí se stránka přehled Azure Active Directory.

3. Na stránce Přehled vyberte **omezení tenanta**.

Správce pro tenanta, který je zadaný jako tenant s omezeným přístupem, může pomocí této sestavy zobrazit blokované přihlášení kvůli zásadám omezení tenanta, včetně použité identity a ID cílového adresáře. Přihlášení jsou obsažena v případě, že je nastavení klienta toto omezení buď tenant klienta, nebo tenant prostředků pro přihlášení.

> [!NOTE]
> Sestava může obsahovat omezené informace, jako je ID cílového adresáře, když se uživatel, který je v jiném klientovi než se přihlásí jako tenant s omezeným přístupem. V takovém případě jsou uživatelsky identifikovatelné informace, jako je název a hlavní název uživatele, maskovány pro ochranu uživatelských dat v jiných klientech (" 00000000-0000-0000-0000-00000000@domain.com "). 

Podobně jako u jiných sestav v Azure Portal můžete použít filtry k určení rozsahu sestavy. Můžete filtrovat podle určitého časového intervalu, uživatele, aplikace, klienta nebo stavu. Pokud vyberete tlačítko **sloupce** , můžete se rozhodnout zobrazit data s libovolnou kombinací následujících polí:

- **Uživatel**
- **Aplikace**
- **Stav**
- **Date** (Datum)
- **Datum (UTC)** (kde UTC je koordinovaný světový čas)
- **IP adresa**
- **Klient**
- **Uživatelské jméno**
- **Umístění**
- **ID cílového tenanta**

## <a name="microsoft-365-support"></a>Podpora Microsoftu 365

Aby aplikace Microsoft 365 plně podporovaly omezení klientů, musí splňovat dvě kritéria:

1. Použitý klient podporuje moderní ověřování.
2. Moderní ověřování je povolené jako výchozí ověřovací protokol pro cloudovou službu.

Nejnovější informace o tom, které klienty Office aktuálně podporují moderní ověřování, najdete v tématu [aktualizované moderní ověřování Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) . Tato stránka obsahuje také odkazy na pokyny pro povolení moderního ověřování pro konkrétní klienty Exchange Online a Skype pro firmy online. SharePoint Online už ve výchozím nastavení umožňuje moderní ověřování.

Microsoft 365 aplikace založené na prohlížeči (portál Office, Yammer, weby SharePointu, Outlook na webu a další) aktuálně podporují omezení tenanta. Silné klienty (Outlook, Skype pro firmy, Word, Excel, PowerPoint a další) můžou vymáhat omezení tenanta jenom v případě, že se používá moderní ověřování.  

Klienti Outlooku a Skype pro firmy, kteří podporují moderní ověřování, můžou pořád používat starší protokoly pro klienty, u kterých není povolené moderní ověřování, a efektivně tak obejít omezení tenanta. Omezení tenanta můžou blokovat aplikace, které používají starší protokoly, pokud při ověřování kontaktují login.microsoftonline.com, login.microsoft.com nebo login.windows.net.

V případě Outlooku ve Windows se zákazníci můžou rozhodnout implementovat omezení, která brání koncovým uživatelům v přidávání neschválených poštovních účtů do jejich profilů. Přečtěte si například nastavení zásad skupiny [Zakázat přidávání jiných než výchozích účtů Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testování

Pokud si chcete vyzkoušet omezení tenanta před implementací pro celou organizaci, máte dvě možnosti: přístup založený na hostiteli pomocí nástroje, jako je Fiddler, nebo dvoufázové zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pro přístup založený na hostiteli

Fiddler je bezplatný proxy server pro ladění, který se dá použít k zachycení a úpravám přenosů HTTP/HTTPS, včetně vkládání hlaviček protokolu HTTP. Pokud chcete nakonfigurovat Fiddler pro testování omezení tenanta, proveďte následující kroky:

1. [Stáhněte a nainstalujte Fiddler](https://www.telerik.com/fiddler).

2. Nakonfigurujte Fiddler pro dešifrování provozu HTTPS, [dokumentace k nápovědě pro Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Nakonfigurovat Fiddler pro vložení hlaviček *omezení přístupu k tenantovi* a *omezení přístupu kontextu* pomocí vlastních pravidel:

   1. V nástroji Fiddler webového ladicího programu vyberte nabídku **pravidla** a vyberte **přizpůsobit pravidla...** pro otevření souboru CustomRules.

   2. Na začátek funkce přidejte následující řádky `OnBeforeRequest` . Nahraďte \<List of tenant identifiers\> doménou registrovanou pro vašeho tenanta (například `contoso.onmicrosoft.com` ). Nahraďte \<directory ID\> identifikátorem GUID Azure AD vašeho tenanta.  Aby se protokoly zobrazovaly ve vašem tenantovi, **musíte** zahrnout správný identifikátor GUID. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

Pokud potřebujete povolení více tenantů, oddělte názvy klientů čárkou. Například:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Uložte a zavřete soubor CustomRules.

Po nakonfigurování Fiddler můžete zachytit provoz tak, že kliknete na nabídku **soubor** a vyberete možnost **zachytit přenos**.

### <a name="staged-rollout-of-proxy-settings"></a>Dvoufázové zavedení nastavení proxy serveru

V závislosti na možnostech vaší infrastruktury proxy může být možné připravit zavedení nastavení pro uživatele. Tady je několik možností vysoké úrovně, které je potřeba zvážit:

1. Soubory PAC můžete použít k ukázání testovacích uživatelů na infrastrukturu testovacího proxy serveru, zatímco normální uživatelé pokračují v používání infrastruktury produkčního proxy serveru.
2. Některé proxy servery můžou podporovat různé konfigurace pomocí skupin.

Konkrétní podrobnosti najdete v dokumentaci k proxy server.

## <a name="blocking-consumer-applications"></a>Blokování aplikací příjemce

Aplikace od Microsoftu, které podporují účty uživatelů a účty organizace, jako je [OneDrive](https://onedrive.live.com/) nebo [Microsoft Learn](https://docs.microsoft.com/learn/), můžou být někdy hostované na stejné adrese URL.  To znamená, že uživatelé, kteří musí mít přístup k této adrese URL pro účely práce, mají také přístup k osobnímu použití, což nemusí být povoleno v souladu s pokyny pro váš operační systém.

Některé organizace se pokoušejí tuto chybu opravit tak, že blokují `login.live.com` pro blokování ověřování osobních účtů.  Má několik downsides:

1. Blok blokuje `login.live.com` použití osobních účtů ve scénářích hostů v B2B, které se můžou intrude na návštěvníky a spolupráci.
1. [Autopilot vyžaduje použití `login.live.com` ](https://docs.microsoft.com/mem/autopilot/networking-requirements) aby bylo možné nasadit nástroj. Scénáře Intune a autopilotu můžou selhat `login.live.com` , když je blokované.
1. Pracovní telemetrie a aktualizace Windows, které spoléhají na službu MSA pro ID zařízení, [budou přestat fungovat](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>Konfigurace pro spotřebitelské aplikace

I když `Restrict-Access-To-Tenants` záhlaví funguje jako seznam povolených, MSA blok funguje jako signál odepřít a oznamuje účet Microsoft platformě, aby se uživatelům neumožnilo přihlašovat se k aplikacím příjemce. K odeslání tohoto signálu `sec-Restrict-Tenant-Access-Policy` se hlavička vloží do provozu s `login.live.com` využitím stejného firemního proxy serveru nebo brány firewall. [](#proxy-configuration-and-requirements) Hodnota hlavičky musí být `restrict-msa` . Když je hlavička přítomná a aplikace příjemce se pokouší přihlašovat přímo k uživateli, toto přihlášení se zablokuje.

V tuto chvíli se ověřování u uživatelských aplikací nezobrazuje v [protokolech správce](#admin-experience), protože Login.Live.com se hostuje nezávisle na službě Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>Co záhlaví dělá a neblokuje

`restrict-msa`Zásady blokují použití uživatelských aplikací, ale umožňují prostřednictvím několika dalších typů provozu a ověřování:

1. Přenos bez uživatele pro zařízení.  To zahrnuje přenos dat pro autopilot, web Windows Update a telemetrie organizace.
1. Ověřování B2B u uživatelských účtů Uživatelé s účty Microsoft, kteří jsou [pozváni, aby mohli spolupracovat s klientem](https://docs.microsoft.com/azure/active-directory/external-identities/redemption-experience#invitation-redemption-flow) ověřit Login.Live.com pro přístup k tenantovi prostředků.
    1. Tento přístup je řízený pomocí `Restrict-Access-To-Tenants` hlavičky pro povolení nebo odepření přístupu k tomuto tenantovi prostředků.
1. "Průchozí" ověřování používané mnoha aplikacemi Azure i Office.com, kde aplikace používají Azure AD k podepisování uživatelů spotřebitelů v kontextu příjemce.
    1. Tento přístup je také řízen pomocí `Restrict-Access-To-Tenants` záhlaví, aby bylo možné povolit nebo odepřít přístup ke speciálnímu tenantovi ( `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ).  Pokud se tento tenant neobjeví ve vašem `Restrict-Access-To-Tenants` seznamu povolených domén, budou se v rámci přihlášení k těmto aplikacím službě Azure AD blokovat uživatelské účty.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [aktualizovaném moderním ověřování pro Office 365](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) .
- Kontrola [adres URL a rozsahů IP adres Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
