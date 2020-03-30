---
title: Použití omezení tenanta ke správě přístupu k aplikacím SaaS – Azure AD
description: Jak pomocí omezení tenanta spravovat, kteří uživatelé mají přístup k aplikacím na základě jejich tenanta Azure AD.
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
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481173"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Správa přístupu ke cloudovým aplikacím SaaS pomocí omezení tenanta

Velké organizace, které kladou důraz na zabezpečení, se chtějí přesunout do cloudových služeb, jako je Office 365, ale potřebují vědět, že jejich uživatelé mají přístup jenom ke schváleným prostředkům. Společnosti tradičně omezují názvy domén nebo adresy IP, pokud chtějí spravovat přístup. Tento přístup selže ve světě, kde jsou aplikace softwaru jako služby (nebo SaaS) hostovány ve veřejném cloudu a běží na sdílených doménových jménech, jako jsou [outlook.office.com](https://outlook.office.com/) a [login.microsoftonline.com](https://login.microsoftonline.com/). Blokování těchto adres by uživatelům bránilo v úplném přístupu k aplikaci Outlook na webu, místo aby je pouze omezovali na schválené identity a prostředky.

Řešení Azure Active Directory (Azure AD) pro tuto výzvu je funkce s názvem omezení tenanta. S omezeními tenanta, organizace můžete řídit přístup ke cloudovým aplikacím SaaS, na základě tenanta Azure AD aplikace používají pro jednotné přihlašování. Můžete například povolit přístup k aplikacím Office 365 vaší organizace a zároveň zabránit přístupu k instancím jiných organizací těchto stejných aplikací.  

S omezeními tenanta organizace můžete určit seznam klientů, které jejich uživatelé mohou přistupovat. Azure AD pak uděluje pouze přístup k těmto povoleným klientům.

Tento článek se zaměřuje na omezení tenanta pro Office 365, ale funkce by měla fungovat s jakoukoli cloudovou aplikací SaaS, která používá moderní ověřovací protokoly s Azure AD pro jednotné přihlašování. Pokud používáte aplikace SaaS s jiným klientem Azure AD z tenanta používaného Office 365, ujistěte se, že jsou povoleny všechny požadované klienty. Další informace o cloudových aplikacích SaaS najdete na [webu Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Jak to funguje

Celkové řešení zahrnuje následující součásti:

1. **Azure AD**: `Restrict-Access-To-Tenants: <permitted tenant list>` Pokud je k dispozici, Azure AD pouze vydává tokeny zabezpečení pro povolené klienty.

2. **Místní infrastruktura proxy serveru**: Tato infrastruktura je proxy zařízení schopné kontroly zabezpečení transportní vrstvy (TLS). Je nutné nakonfigurovat proxy vložit záhlaví obsahující seznam povolených klientů do provozu určeného pro Azure AD.

3. **Klientský software**: Chcete-li podporovat omezení klienta, klientský software musí požadovat tokeny přímo z Azure AD, aby proxy infrastruktury může zachytit provoz. Aplikace Office 365 založené na prohlížeči v současné době podporují omezení tenanta, stejně jako klienti Office, kteří používají moderní ověřování (například OAuth 2.0).

4. **Moderní ověřování**: Cloudové služby musí používat moderní ověřování k použití omezení klienta a blokovat přístup ke všem nepovoleným klientům. Cloudové služby Office 365 je nutné nakonfigurovat tak, aby ve výchozím nastavení používaly moderní ověřovací protokoly. Nejnovější informace o podpoře Office 365 pro moderní ověřování [našetřujete na článek Aktualizované moderní ověřování Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Následující diagram znázorňuje tok provozu na vysoké úrovni. Omezení tenanta vyžaduje kontrolu TLS jenom na provoz do Azure AD, ne na cloudové služby Office 365. Toto rozlišení je důležité, protože objem provozu pro ověřování ve službě Azure AD je obvykle mnohem nižší než objem provozu do aplikací SaaS, jako je Exchange Online a SharePoint Online.

![Dopravní tok klienta omezení – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Nastavení omezení klienta

Existují dva kroky, jak začít s omezeními tenanta. Nejprve se ujistěte, že se vaši klienti mohou připojit ke správným adresám. Za druhé, nakonfigurujte infrastrukturu proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL a IP adresy

Chcete-li použít omezení klienta, musí být vaši klienti schopni se připojit k následujícím adresám URL Azure AD k ověření: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)a [login.windows.net](https://login.windows.net/). Chcete-li získat přístup k Office 365, musí se navíc klienti připojit k plně kvalifikovaným názvům domén (Plně kvalifikovaným doménovým souborům), adresám URL a adresám IP definovaným v [adresách URL office 365 a v rozsahu IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfigurace a požadavky proxy serveru

Následující konfigurace je vyžadována k povolení omezení klienta prostřednictvím infrastruktury proxy. Tento návod je obecný, takže byste měli odkazovat na dokumentaci dodavatele proxy pro konkrétní kroky implementace.

#### <a name="prerequisites"></a>Požadavky

- Proxy server musí být schopen provádět zachycení TLS, vkládání hlavičky HTTP a cíle filtrování pomocí souborů FQDN/URL.

- Klienti musí důvěřovat řetězu certifikátů prezentovanému proxy serverem pro komunikaci TLS. Pokud jsou například použity certifikáty z interní [infrastruktury veřejných klíčů (PKI),](/windows/desktop/seccertenroll/public-key-infrastructure) musí být interní certifikát kořenové certifikační autority, který vystavuje, důvěryhodný.

- Tato funkce je součástí předplatných Office 365, ale pokud chcete použít omezení tenanta k řízení přístupu k jiným aplikacím SaaS, pak jsou vyžadovány licence Azure AD Premium 1.

#### <a name="configuration"></a>Konfigurace

Pro každý příchozí požadavek na login.microsoftonline.com, login.microsoft.com a login.windows.net vložte dvě hlavičky HTTP: *Omezení přístupu k klientům* a *Omezení kontextu přístupu*.

Záhlaví by měla obsahovat následující prvky:

- Pro *možnost Omezit přístup k klientům*použijte \<hodnotu\>seznamu povolených klientů , což je seznam klientů oddělených čárkami, který má uživatelům umožnit přístup. Libovolná doména, která je registrována u klienta, slouží k identifikaci klienta v tomto seznamu. Chcete-li například povolit přístup k klientům contoso a fabrikam, vypadá dvojice název/hodnota takto: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Pro *omezení přístupu k kontextu*použijte hodnotu id jednoho adresáře, deklarující, který tenant nastavuje omezení klienta. Chcete-li například deklarovat Contoso jako klienta, který nastavil zásady omezení klienta, vypadá dvojice název/hodnota takto: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> ID adresáře najdete na [portálu Azure Active Directory](https://aad.portal.azure.com/). Přihlaste se jako správce, vyberte **Azure Active Directory**a vyberte **Vlastnosti**.

Chcete-li zabránit uživatelům v vložení vlastní hlavičky HTTP s neschválenými klienty, proxy server musí nahradit hlavičku *Omezit přístup k klientům,* pokud je již v příchozím požadavku k dispozici.

Klienti musí být nuceni používat proxy server pro všechny požadavky na login.microsoftonline.com, login.microsoft.com a login.windows.net. Pokud se například soubory PAC používají k nasměrování klientů na použití proxy serveru, koncoví uživatelé by neměli mít možnost upravovat nebo zakazovat soubory PAC.

## <a name="the-user-experience"></a>Uživatelské prostředí

Tato část popisuje prostředí pro koncové uživatele i správce.

### <a name="end-user-experience"></a>Prostředí koncového uživatele

Příklad uživatele je v síti Contoso, ale pokouší se získat přístup k instanci Fabrikam sdílené aplikace SaaS, jako je Outlook online. Pokud je společnost Fabrikam nepovoleným tenantem instance Contoso, uživatel i ono vidí zprávu o odmítnutí přístupu, která říká, že se pokoušíte získat přístup k prostředku, který patří organizaci neschválené vaším oddělením IT.

### <a name="admin-experience"></a>Prostředí pro správu

Zatímco konfigurace omezení tenanta se provádí na infrastruktuře podnikového proxy serveru, správci mají přístup k sestavám omezení klienta na webu Azure Portal přímo. Zobrazení sestav:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se řídicí panel **Centra pro správu Služby Azure Active Directory.**

2. V levém podokně vyberte **Azure Active Directory**. Zobrazí se stránka přehledu služby Azure Active Directory.

3. V nadpisu **Další možnosti** vyberte **omezení klienta**.

Správce pro tenanta zadaný jako klient s omezeným přístupem může tuto sestavu použít k zobrazení přihlášení blokovaných z důvodu zásad omezení klienta, včetně použité identity a ID cílového adresáře. Přihlášení jsou zahrnuty, pokud klient nastavení omezení je buď klient a klient a klient a klient a klient a klient a klient a klient a klient a klient a klient a klient a klient pro přihlášení.

> [!NOTE]
> Sestava může obsahovat omezené informace, například ID cílového adresáře, když se přihlašuje uživatel, který je v jiném tenantovi než v kontextu omezeného přístupu. V tomto případě jsou informace identifikovatelné uživatelem, jako je jméno a hlavní jméno uživatele, maskovány za účelem ochrany uživatelských dat v jiných klientech.

Stejně jako ostatní sestavy na webu Azure Portal můžete pomocí filtrů určit rozsah sestavy. Můžete filtrovat na určitý časový interval, uživatele, aplikace, klienta nebo stav. Pokud vyberete tlačítko **Sloupce,** můžete zvolit zobrazení dat s libovolnou kombinací následujících polí:

- **Uživatel**
- **Aplikace**
- **Stav**
- **Datum**
- **Datum (UTC)** (kde UTC je koordinovaný světový čas)
- **Metoda ověření Vícefaktorové** (metoda vícefaktorového ověřování)
- Detail ověření více **faktorem** (vícefaktorové ověřování)
- **Výsledek vícefaktorové hodu**
- **IP adresa**
- **Klienta**
- **Username**
- **Umístění**
- **ID cílového klienta**

## <a name="office-365-support"></a>Podpora Office 365

Aplikace Office 365 musí splňovat dvě kritéria, aby bylo nutné plně podporovat omezení klientů:

1. Použitý klient podporuje moderní ověřování.
2. Moderní ověřování je povoleno jako výchozí ověřovací protokol pro cloudovou službu.

Nejnovější informace, na kterých klienti Office aktuálně podporují moderní ověřování, najdete [v části Aktualizované moderní ověřování Office 365.](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) Tato stránka také obsahuje odkazy na pokyny pro povolení moderního ověřování u konkrétních klientů Exchange Online a Online Skypu pro firmy. SharePoint Online už ve výchozím nastavení povoluje moderní ověřování.

Aplikace office 365 založené na prohlížeči (portál Office, Yammer, weby SharePointu, Outlook na webu a další) v současné době podporují omezení tenanta. Tlustí klienti (Outlook, Skype pro firmy, Word, Excel, PowerPoint a další) můžou vynucovat omezení tenanta jenom při použití moderního ověřování.  

Klienti Outlooku a Skypu pro firmy, kteří podporují moderní ověřování, mohou stále používat starší protokoly proti klientům, kde není povoleno moderní ověřování, což efektivně obchází omezení klientů. Omezení tenanta může blokovat aplikace, které používají starší protokoly, pokud se během ověřování obrátí login.microsoftonline.com, login.microsoft.com nebo login.windows.net.

V aplikaci Outlook v systému Windows se zákazníci mohou rozhodnout implementovat omezení, která koncovým uživatelům zabraňují přidávat do jejich profilů neschválené e-mailové účty. Například v tématu [Zabránit přidávání nevýchozího](https://gpsearch.azurewebsites.net/default.aspx?ref=1) nastavení zásad skupiny účtů Exchange.

## <a name="testing"></a>Testování

Pokud chcete vyzkoušet omezení tenanta před implementací pro celou organizaci, máte dvě možnosti: přístup založený na hostiteli pomocí nástroje, jako je Fiddler, nebo postupné zavedení nastavení proxy serveru.

### <a name="fiddler-for-a-host-based-approach"></a>Houslista pro přístup založený na hostiteli

Fiddler je bezplatný webový ladicí proxy server, který lze použít k zachycení a úpravě přenosu HTTP/HTTPS, včetně vkládání http hlaviček. Chcete-li nakonfigurovat Šumař pro testování omezení klienta, proveďte následující kroky:

1. [Stáhněte si a nainstalujte Fiddler](https://www.telerik.com/fiddler).

2. Konfigurace Fiddler dešifrovat https provoz, za [nápovědu Fiddler dokumentace](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Nakonfigurujte šumavku tak, *aby vklápěla* záhlaví Omezit přístup k klientům a *omezit kontextová* záhlaví pomocí vlastních pravidel:

   1. V nástroji Fiddler Web Debugger vyberte nabídku **Pravidla** a vyberte **Přizpůsobit pravidla...** otevřete soubor CustomRules.

   2. Na začátek `OnBeforeRequest` funkce přidejte následující řádky. \<Nahraďte\> doménu klienta doménou registrovanou u vašeho klienta `contoso.onmicrosoft.com`(například ). Nahraďte \<\> ID adresáře identifikátorem identifikátoru GUID služby Azure AD.

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

      Pokud potřebujete povolit více klientů, použijte čárku k oddělení názvů klientů. Například:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Uložte a zavřete soubor CustomRules.

Po konfiguraci Šumař, můžete zachytit provoz tím, že půjdete do nabídky **Soubor** a výběrem **Zachytit provoz**.

### <a name="staged-rollout-of-proxy-settings"></a>Postupné zavedení nastavení proxy serveru

V závislosti na možnostech infrastruktury proxy serveru můžete uživatelům provést zavedení nastavení. Zde je několik možností na vysoké úrovni ke zvážení:

1. Pomocí souborů PAC můžete uživatele nasměrovat na testovací infrastrukturu proxy serveru, zatímco běžní uživatelé nadále používají infrastrukturu produkčního proxy serveru.
2. Některé proxy servery mohou podporovat různé konfigurace pomocí skupin.

Konkrétní podrobnosti naleznete v dokumentaci k serveru proxy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [aktualizovaném moderním ověřování Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Kontrola [adres URL a rozsahů IP adres Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
