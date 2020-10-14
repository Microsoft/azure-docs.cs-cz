---
title: Migrace z Azure Access Control Service | Microsoft Docs
description: Přečtěte si o možnostech přesunu aplikací a služeb z Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 0f40c91672310d5963dab01180ea92633e970c5c
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055358"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Postupy: migrace z Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), bude vyřazení 7. listopadu 2018. Aplikace a služby, které jsou aktuálně používány Access Control, musí být v rámci této služby plně migrovány na jiný ověřovací mechanismus. Tento článek popisuje doporučení pro stávající zákazníky, protože máte v úmyslu přestat používat Access Control. Pokud v tuto chvíli nepoužíváte Access Control, nemusíte provádět žádnou akci.

## <a name="overview"></a>Přehled

Access Control je služba Cloud Authentication, která nabízí snadný způsob, jak ověřovat a autorizovat uživatele pro přístup k vašim webovým aplikacím a službám. Umožňuje vyhlásit mnoho funkcí ověřování a autorizace z vašeho kódu. Access Control se primárně používají vývojáři a architekti klientů Microsoft .NET, webové aplikace ASP.NET a webové služby Windows Communication Foundation (WCF).

Případy použití pro Access Control můžou být rozdělené do tří hlavních kategorií:

- Ověřování pro určité cloudové služby Microsoftu, včetně Azure Service Bus a Dynamics CRM. Klientské aplikace získávají tokeny od Access Control k ověření pro tyto služby k provádění různých akcí.
- Přidání ověřování do webových aplikací, vlastní i předbaleno (jako SharePoint). Pomocí Access Control pasivního ověřování můžou webové aplikace podporovat přihlášení pomocí účet Microsoft (dřív Live ID) a s účty z Google, Facebook, Yahoo, Azure AD a Active Directory Federation Services (AD FS) (AD FS).
- Zabezpečení vlastních webových služeb s tokeny vydanými Access Control. Pomocí ověřování aktivní webové služby můžou zajistit, že umožní přístup jenom ke známým klientům, kteří jsou ověření pomocí Access Control.

Jednotlivé případy použití a doporučené strategie migrace jsou popsány v následujících částech.

> [!WARNING]
> Ve většině případů se pro migraci stávajících aplikací a služeb na novější technologie vyžadují významné změny kódu. Doporučujeme vám ihned naplánovat plánování a provedení migrace, aby se předešlo případným výpadkům nebo výpadkům.

Access Control má následující součásti:

- Služba tokenů zabezpečení (STS), která přijímá žádosti o ověření a vydává tokeny zabezpečení při návratu.
- Portál Azure Classic, kde můžete vytvářet, odstraňovat a zapínat Access Control obory názvů.
- Samostatný portál pro správu Access Control, kde můžete přizpůsobit a nakonfigurovat Access Control obory názvů.
- Služba správy, kterou můžete použít k automatizaci funkcí portálů.
- Modul pravidel transformace tokenu, který můžete použít k definování složitou logiku pro manipulaci s výstupním formátem tokenů, které Access Control problémy.

Chcete-li použít tyto komponenty, je nutné vytvořit jeden nebo více Access Control oborů názvů. *Obor názvů* je vyhrazená instance Access Control, se kterou vaše aplikace a služby komunikují. Obor názvů je izolovaný od všech ostatních Access Controlch zákazníků. Jiní zákazníci Access Control používají své vlastní obory názvů. Obor názvů v Access Control má vyhrazenou adresu URL, která vypadá takto:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Veškerá komunikace s operacemi služby STS a správou se provádí na této adrese URL. Pro různé účely můžete použít různé cesty. Pokud chcete zjistit, jestli vaše aplikace nebo služby používají Access Control, Sledujte jakýkoliv provoz do &lt; oboru názvů https:// &gt; . AccessControl.Windows.NET. Veškerý provoz na tuto adresu URL se zpracovává pomocí Access Control a musí být vyřazený. 

Výjimkou je jakýkoli provoz na `https://accounts.accesscontrol.windows.net` . Provoz na tuto adresu URL již zpracovává jiná **služba a neovlivňuje Access Control** vyřazení. 

Další informace o Access Control najdete v článku [Access Control Service 2,0 (archivováno)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Zjistěte, na které z vašich aplikací bude mít vliv.

Podle kroků v této části zjistíte, které z vašich aplikací budou ovlivněny vyřazením služby ACS.

### <a name="download-and-install-acs-powershell"></a>Stažení a instalace služby ACS PowerShell

1. Přejít na Galerie prostředí PowerShell a stáhnout službu [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Nainstalujte modul spuštěním

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Získání seznamu všech možných příkazů spuštěním

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Chcete-li získat nápovědu ke konkrétnímu příkazu, spusťte příkaz:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    kde `[Command-Name]` je název příkazu ACS.

### <a name="list-your-acs-namespaces"></a>Seznam oborů názvů služby ACS

1. Připojte se k ACS pomocí rutiny **Connect-AcsAccount** .
  
    Je možné, že budete muset spustit `Set-ExecutionPolicy -ExecutionPolicy Bypass` před spuštěním příkazů a správce těchto předplatných, aby bylo možné příkazy spustit.

2. Seznam dostupných předplatných Azure získáte pomocí rutiny **Get-AcsSubscription** .
3. Uveďte obory názvů služby ACS pomocí rutiny **Get-AcsNamespace** .

### <a name="check-which-applications-will-be-impacted"></a>Ověřte, které aplikace budou ovlivněny.

1. Použijte obor názvů z předchozího kroku a pak přejít na `https://<namespace>.accesscontrol.windows.net`

    Například pokud je jeden z oborů názvů contoso-test, přejít na `https://contoso-test.accesscontrol.windows.net`

2. V části **vztahy důvěryhodnosti**vyberte **aplikace předávající strany** , kde se zobrazí seznam aplikací, které budou ovlivněny vyřazením služby ACS.
3. Opakujte kroky 1-2 pro všechny další obory názvů ACS.

## <a name="retirement-schedule"></a>Plán vyřazení

Od listopadu 2017 jsou všechny součásti Access Control plně podporované a funkční. Jediným omezením je, že [nemůžete vytvářet nové obory názvů Access Control prostřednictvím portálu Azure Classic](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Tady je plán pro zastaralé Access Control komponenty:

- **Listopadu 2017**: prostředí pro správu Azure AD na portálu Azure Classic [je vyřazené](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). V tomto okamžiku je Správa oboru názvů pro Access Control k dispozici na nové vyhrazené adrese URL: `https://manage.windowsazure.com?restoreClassic=true` . Pomocí této adresy URl můžete zobrazit existující obory názvů, povolit a zakázat obory názvů a odstranit obory názvů, pokud se rozhodnete.
- **2. dubna 2018**: portál Azure Classic je zcela vyřazený, což znamená Access Control Správa oboru názvů už není dostupná prostřednictvím žádné adresy URL. V tuto chvíli nemůžete zakázat nebo povolit, odstranit ani vyčíslit Access Control obory názvů. Portál pro správu Access Control ale bude plně funkční a umístěný v `https://\<namespace\>.accesscontrol.windows.net` . Všechny ostatní komponenty Access Control nadále pracují normálně.
- **7. listopadu 2018**: všechny součásti Access Control jsou trvale vypnuté. Patří sem portál pro správu Access Control, služba pro správu, služba tokenů zabezpečení a modul pravidel transformace tokenů. V tomto okamžiku selžou všechny požadavky odeslané na Access Control (umístěné na adrese \<namespace\> . AccessControl.Windows.NET). Před tímto časem byste měli všechny stávající aplikace a služby migrovat do jiných technologií.

> [!NOTE]
> Zásada zakáže obory názvů, u kterých se pro časové období nepožadoval token. Od první září 2018 je tato doba v současnosti ve 14 dnech nečinnosti, ale zkracuje se na 7 dnů nečinnosti v nadcházejících týdnech. Pokud máte Access Control obory názvů, které jsou aktuálně zakázané, můžete [si stáhnout a nainstalovat službu ACS PowerShell a](#download-and-install-acs-powershell) znovu povolit obory názvů.

## <a name="migration-strategies"></a>Strategie migrace

V následujících částech jsou popsána doporučení vysoké úrovně pro migraci z Access Control na jiné technologie společnosti Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienti cloudových služeb Microsoftu

Každá cloudová služba Microsoftu, která přijímá tokeny vydané Access Control nyní podporuje alespoň jednu alternativní formu ověřování. Správný ověřovací mechanismus se u každé služby liší. Doporučujeme, abyste si pro každou službu poodkazovali na konkrétní dokumentaci pro oficiální pokyny. Pro usnadnění práce je k dispozici každá sada dokumentace:

| Služba | Pokyny |
| ------- | -------- |
| Azure Service Bus | [Migrace na sdílené přístupové podpisy](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migrace na sdílené přístupové podpisy](../../azure-relay/relay-migrate-acs-sas.md) |
| Spravovaná mezipaměť Azure | [Migrace na Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migrace na rozhraní API služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrace na funkci Logic Apps Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migrace na ověřování Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Upgrade agenta Azure Backup](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Zákazníci SharePoint

Zákazníci SharePoint 2013, 2016 a SharePointu Online používali službu ACS pro účely ověřování v cloudu, místních i hybridních scénářích. Některé funkce SharePointu a případy použití budou ovlivněny vyřazením služby ACS, i když jiné nebudou. Níže uvedená tabulka shrnuje pokyny k migraci pro některé z nejoblíbenějších funkcí SharePointu, které využívají ACS:

| Funkce | Pokyny |
| ------- | -------- |
| Ověřují se uživatelé z Azure AD | Dřív služba Azure AD nepodporovala tokeny SAML 1,1 vyžadované službou SharePoint pro ověřování a služba ACS byla použita jako prostředník, který provedl SharePoint kompatibilní s formáty tokenů Azure AD. Teď můžete [SharePoint připojit přímo ke službě Azure AD pomocí aplikace Azure AD Galerie služby SharePoint v místní aplikaci](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [Ověřování aplikací & ověřování serveru ve službě SharePoint místně](/SharePoint/security-for-sharepoint-server/authentication-overview) | Neovlivněné vyřazením služby ACS; nejsou nutné žádné změny. | 
| [Ověřování s nízkou důvěryhodností pro doplňky pro SharePoint (hostované na poskytovatelích a hostované na SharePointu)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Neovlivněné vyřazením služby ACS; nejsou nutné žádné změny. |
| [Hybridní hledání cloudu služby SharePoint](/archive/blogs/spses/cloud-hybrid-search-service-application) | Neovlivněné vyřazením služby ACS; nejsou nutné žádné změny. |

### <a name="web-applications-that-use-passive-authentication"></a>Webové aplikace, které používají pasivní ověřování

U webových aplikací, které používají Access Control pro ověřování uživatelů, Access Control poskytuje vývojářům a architektům webových aplikací následující funkce a možnosti:

- Hluboká integrace s Windows Identity Foundation (WIF).
- Federaci s účty Google, Facebook, Yahoo, Azure Active Directory a AD FS a účty Microsoft.
- Podpora pro následující protokoly ověřování: OAuth 2,0, WS-Trust a specifikace Web Services Federation (WS-Federation).
- Podpora pro následující formáty tokenů: JSON Web Token (JWT), SAML 1,1, SAML 2,0 a Simple web token (SWT).
- Prostředí pro zjišťování domovské sféry integrované do WIF, které umožňuje uživatelům vybrat typ účtu, který používají pro přihlášení. Toto prostředí hostuje webová aplikace a je plně přizpůsobitelná.
- Transformace tokenu, která umožňuje bohatě přizpůsobit deklarace identity přijaté webovou aplikací z Access Control, včetně:
    - Předejte deklarace identity od zprostředkovatelů identity.
    - Přidávají se další vlastní deklarace identity.
    - Jednoduchá logika if-then pro vystavování deklarací za určitých podmínek.

Bohužel není jedna služba, která nabízí všechny tyto ekvivalentní funkce. Měli byste vyhodnotit, které funkce Access Control potřebujete, a pak zvolit mezi používáním [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) nebo jiné služby ověřování v cloudu.

#### <a name="migrate-to-azure-active-directory"></a>Migrace na Azure Active Directory

Cesta, která se má vzít v úvahu, integruje vaše aplikace a služby přímo s Azure AD. Azure AD je cloudový poskytovatel identity pro pracovní nebo školní účty Microsoftu. Azure AD je poskytovatel identity pro Microsoft 365, Azure a spoustu dalších věcí. Poskytuje podobné možnosti federovaného ověřování Access Control, ale nepodporuje všechny funkce Access Control. 

Primárním příkladem je federace se zprostředkovateli sociálních identit, jako je Facebook, Google a Yahoo. Pokud se uživatelé přihlásí pomocí těchto typů přihlašovacích údajů, Azure AD není pro vás řešení. 

Azure AD také nutně nepodporuje tytéž stejné ověřovací protokoly jako Access Control. Například i když má protokol OAuth podporu Access Control i Azure AD, existují drobné rozdíly mezi jednotlivými implementacemi. Různé implementace vyžadují, abyste v rámci migrace změnili kód.

Azure AD ale poskytuje několik potenciálních výhod Access Control zákazníkům. Nativně podporuje pracovní nebo školní účty, které jsou hostovány v cloudu, které jsou běžně používány Access Control zákazníky. 

Tenant Azure AD může být taky federovaný na jednu nebo víc instancí místní služby Active Directory prostřednictvím AD FS. Tímto způsobem může vaše aplikace ověřovat cloudové uživatele a uživatele hostované místně. Podporuje také protokol WS-Federation, který umožňuje jejich integraci s webovou aplikací, a to pomocí WIF.

Následující tabulka porovnává funkce Access Control, které jsou relevantní pro webové aplikace, s funkcemi dostupnými ve službě Azure AD. 

*Pokud uživatelům umožníte, aby se přihlásili jenom s pracovními nebo školními účty Microsoftu, je Azure Active Directory pravděpodobně nejlepší volbou pro vaši migraci*.

| Schopnost | Podpora Access Control | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Pracovní nebo školní účty Microsoftu | Podporováno | Podporováno |
| Účty z Windows Server Active Directory a AD FS |– Podporováno prostřednictvím federace s klientem služby Azure AD <br />– Podporováno prostřednictvím přímé federace s AD FS | Podporuje se jenom přes federaci s tenant Azure AD. | 
| Účty z jiných systémů pro správu podnikových identit |– Možné prostřednictvím federace s klientem služby Azure AD <br />– Podporováno prostřednictvím přímé federace | Možné prostřednictvím federace s klientem služby Azure AD |
| Účty Microsoft pro osobní použití | Podporováno | Podporováno prostřednictvím protokolu OAuth služby Azure AD v 2.0, ale nikoli přes žádné jiné protokoly. | 
| Účty Facebook, Google, Yahoo | Podporováno | Nepodporováno |
| **Protokoly a kompatibilita sady SDK** | | |
| WIF | Podporováno | Podporované, ale k dispozici jsou omezené pokyny |
| WS-Federation | Podporováno | Podporováno |
| OAuth 2.0 | Podpora pro koncept 13 | Podpora pro specifikaci RFC 6749, nejvíce moderní specifikace |
| WS-Trust | Podporováno | Nepodporováno |
| **Formáty tokenů** | | |
| TOKEN | Podporováno ve verzi beta | Podporováno |
| SAML 1,1 | Podporováno | Preview |
| SAML 2.0 | Podporováno | Podporováno |
| SWT | Podporováno | Nepodporováno |
| **Vlastní nastavení** | | |
| Přizpůsobitelná funkce/zjišťování domovské sféry/účet – výdejové rozhraní | Kód ke stažení, který se dá začlenit do aplikací | Nepodporováno |
| Nahrávat vlastní podpisové certifikáty tokenů | Podporováno | Podporováno |
| Přizpůsobení deklarací identity v tokenech |-Předávací deklarace vstupu od zprostředkovatelů identity<br />– Získat přístupový token od zprostředkovatele identity jako deklaraci identity<br />– Vydávání deklarací výstupních dat na základě hodnot vstupních deklarací identity<br />– Vydávání deklarací výstupů s konstantními hodnotami |-Nelze předat deklarace identity od zprostředkovatelů federovaných identit.<br />– Nejde získat přístupový token od zprostředkovatele identity jako deklaraci identity.<br />-Nelze vystavit deklarace výstupů na základě hodnot vstupních deklarací identity.<br />– Může vydávat deklarace výstupů s konstantními hodnotami.<br />– Může vystavovat deklarace identity na základě vlastností uživatelů synchronizovaných do Azure AD. |
| **Automation** | | |
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby správy Access Control | Podporováno pomocí rozhraní Microsoft Graph API |

Pokud se rozhodnete, že Azure AD je nejlepší migrační cestou k vašim aplikacím a službám, měli byste si být vědomi dvou způsobů integrace aplikace s Azure AD.

Pokud chcete pro integraci se službou Azure AD použít WS-Federation nebo WIF, doporučujeme postupovat podle postupu popsaného v tématu [Konfigurace federovaného jednotného přihlašování pro aplikaci mimo galerii](../manage-apps/configure-saml-single-sign-on.md). Tento článek se týká konfigurace Azure AD pro jednotné přihlašování založené na SAML, ale funguje taky pro konfiguraci WS-Federation. Následující přístup vyžaduje licenci Azure AD Premium. Tento přístup má dvě výhody:

- Získáte plnou flexibilitu přizpůsobení tokenů Azure AD. Deklarace identity, které vydává služba Azure AD, můžete přizpůsobit tak, aby odpovídaly deklaracím, které vydávají Access Control. To zahrnuje zejména ID uživatele nebo deklaraci identity identifikátoru názvu. Pokud chcete pro uživatele po změně technologií nadále dostávat konzistentní identifikátory uživatelů, zajistěte, aby ID uživatelů vydaná službou Azure AD odpovídala vydaným Access Control.
- Můžete nakonfigurovat podpisový certifikát tokenu, který je specifický pro vaši aplikaci, a s dobou životnosti, kterou ovládáte.

> [!NOTE]
> Tento přístup vyžaduje licenci Azure AD Premium. Pokud jste zákazník Access Control a potřebujete licenci Premium pro nastavení jednotného přihlašování pro aplikaci, kontaktujte nás. Budeme vám poskytovat licence pro vývojáře, které vám umožníme využít.

Alternativním řešením je postupovat v [této ukázce kódu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), který poskytuje mírně odlišné pokyny k nastavení WS-Federation. Tato ukázka kódu nepoužívá WIF, ale místo toho middleware ASP.NET 4,5 OWIN. Pokyny pro registraci aplikace jsou ale platné pro aplikace využívající WIF a nevyžadují licenci Azure AD Premium. 

Pokud si vyberete tento přístup, budete muset pochopit, jak se má [Služba Azure AD vyměnit při podepisování klíčů](../develop/active-directory-signing-key-rollover.md). Tento přístup používá k vydávání tokenů globální podepisovací klíč Azure AD. WIF ve výchozím nastavení automaticky neaktualizuje podpisové klíče. Když Azure AD otočí své globální podpisové klíče, musí být vaše implementace WIF připravená, aby tyto změny přijímala. Další informace najdete v tématu [důležité informace o výměně klíčů v Azure AD](/previous-versions/azure/dn641920(v=azure.100)).

Pokud můžete integrovat se službou Azure AD prostřednictvím protokolů OpenID Connect nebo OAuth, doporučujeme to udělat. Máme rozsáhlou dokumentaci a pokyny, jak integrovat Azure AD do vaší webové aplikace, která je dostupná v naší [příručce pro vývojáře Azure AD](../develop/index.yml).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrace na Azure Active Directory B2C

Další cestou k migraci, která se má zvážit, je Azure AD B2C. Azure AD B2C je cloudová služba ověřování, která jako Access Control umožňuje vývojářům využít logiku ověřování a správy identit v cloudové službě. Je to placená služba (s úrovněmi Free a Premium), která je určená pro zákaznické aplikace, které můžou mít až milionů aktivních uživatelů.

Podobně jako Access Control, jedna z nejoblíbenějších funkcí Azure AD B2C je, že podporuje mnoho různých typů účtů. Pomocí Azure AD B2C můžete uživatele přihlašovat pomocí svých účet Microsoftů nebo účtů Facebook, Google, LinkedIn, GitHubu nebo Yahoo a dalších. Azure AD B2C podporuje také "místní účty" nebo uživatelské jméno a hesla, které uživatelé vytvářejí speciálně pro vaši aplikaci. Azure AD B2C také poskytuje bohatou rozšiřitelnost, kterou můžete použít k přizpůsobení toků přihlášení. 

Azure AD B2C ale nepodporuje širokou škálu protokolů pro ověřování a formátů tokenů, které Access Control můžou zákazníci potřebovat. Azure AD B2C také nemůžete použít k získání tokenů a dotazování na Další informace o uživateli od poskytovatele identity, Microsoft nebo jinak.

Následující tabulka porovnává funkce Access Control, které jsou relevantní pro webové aplikace s funkcemi dostupnými v Azure AD B2C. Na nejvyšší úrovni *je Azure AD B2C pravděpodobně správnou volbou pro vaši migraci, pokud je vaše aplikace spotřebiteli nebo pokud podporuje mnoho různých typů účtů.*

| Schopnost | Podpora Access Control | Podpora Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Pracovní nebo školní účty Microsoftu | Podporováno | Podporováno prostřednictvím vlastních zásad  |
| Účty z Windows Server Active Directory a AD FS | Podporováno prostřednictvím přímé federace s AD FS | Podporováno prostřednictvím federace SAML pomocí vlastních zásad |
| Účty z jiných systémů pro správu podnikových identit | Podporováno prostřednictvím přímé federace prostřednictvím WS-Federation | Podporováno prostřednictvím federace SAML pomocí vlastních zásad |
| Účty Microsoft pro osobní použití | Podporováno | Podporováno | 
| Účty Facebook, Google, Yahoo | Podporováno | Facebook a Google podporované nativně, Yahoo prostřednictvím služby OpenID Connect Federation pomocí vlastních zásad |
| **Protokoly a kompatibilita sady SDK** | | |
| Windows Identity Foundation (WIF) | Podporováno | Nepodporováno |
| WS-Federation | Podporováno | Nepodporováno |
| OAuth 2.0 | Podpora pro koncept 13 | Podpora pro specifikaci RFC 6749, nejvíce moderní specifikace |
| WS-Trust | Podporováno | Nepodporováno |
| **Formáty tokenů** | | |
| TOKEN | Podporováno ve verzi beta | Podporováno |
| SAML 1,1 | Podporováno | Nepodporováno |
| SAML 2.0 | Podporováno | Nepodporováno |
| SWT | Podporováno | Nepodporováno |
| **Vlastní nastavení** | | |
| Přizpůsobitelná funkce/zjišťování domovské sféry/účet – výdejové rozhraní | Kód ke stažení, který se dá začlenit do aplikací | Plně přizpůsobitelné uživatelské rozhraní prostřednictvím vlastních šablon stylů CSS |
| Nahrávat vlastní podpisové certifikáty tokenů | Podporováno | Vlastní podpisové klíče, nikoli certifikáty, podporované prostřednictvím vlastních zásad |
| Přizpůsobení deklarací identity v tokenech |-Předávací deklarace vstupu od zprostředkovatelů identity<br />– Získat přístupový token od zprostředkovatele identity jako deklaraci identity<br />– Vydávání deklarací výstupních dat na základě hodnot vstupních deklarací identity<br />– Vydávání deklarací výstupů s konstantními hodnotami |– Může předávat deklarace identity od zprostředkovatelů identity; vlastní zásady vyžadované pro některé deklarace identity<br />– Nejde získat přístupový token od zprostředkovatele identity jako deklaraci identity.<br />– Může vydávat deklarace výstupů na základě hodnot vstupních deklarací prostřednictvím vlastních zásad.<br />– Může vystavovat deklarace výstupů s konstantními hodnotami prostřednictvím vlastních zásad. |
| **Automation** | | |
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby správy Access Control |-Vytváření uživatelů s povoleným použitím rozhraní Microsoft Graph API<br />-Nelze vytvořit klienty B2C, aplikace nebo zásady prostřednictvím kódu programu. |

Pokud se rozhodnete, že Azure AD B2C je nejlepší cestou migrace pro vaše aplikace a služby, začněte s následujícími prostředky:

- [Dokumentace k Azure AD B2C](../../active-directory-b2c/overview.md)
- [Vlastní zásady Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Ceny Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na test identity nebo Auth0

V některých případech se může stát, že Azure AD a Azure AD B2C nestačí nahradit Access Control ve vašich webových aplikacích, aniž byste museli provádět zásadní změny kódu. Mezi běžné příklady můžou patřit:

- Webové aplikace, které používají WIF nebo WS-Federation pro přihlášení pomocí poskytovatelů sociálních identit, jako je Google nebo Facebook.
- Webové aplikace, které provádějí přímou federaci zprostředkovateli identity organizace prostřednictvím WS-Federationho protokolu.
- Webové aplikace, které vyžadují přístupový token vydaný poskytovatelem sociální identity (například Google nebo Facebook) jako deklarace v tokenech vydaných Access Control.
- Webové aplikace s pravidly transformace složitého tokenu, které Azure AD nebo Azure AD B2C nemůžou reprodukovány.
- Víceklientské webové aplikace, které využívají službu ACS k centrální správě federace pro celou řadu různých zprostředkovatelů identity

V těchto případech je vhodné zvážit migraci webové aplikace do jiné služby Cloud Authentication. Doporučujeme prozkoumat následující možnosti. Každá z následujících možností nabízí podobné funkce jako Access Control:

![Na tomto obrázku vidíte logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) je flexibilní cloudová služba identit, která [pro zákazníky Access ControlA vytvořila pokyny k migraci na vysoké úrovni](https://auth0.com/acs)a podporuje téměř všechny funkce služby ACS.

![Tento obrázek ukazuje logo identity testu.](./media/active-directory-acs-migration/rsz-ping.png)

Služba [příkazem testovat identitu](https://www.pingidentity.com) nabízí dvě řešení podobná službě ACS. PingOne je cloudová služba identit, která podporuje mnoho stejných funkcí jako ACS a PingFederate je podobný místní produkt identity, který nabízí větší flexibilitu. Další podrobnosti o používání těchto produktů najdete v tématu [pokyny k vyřazení služby ACS](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) pomocí nástroje.

Naším cílem při práci s identitou a nástrojem Auth0 je zajistit, aby měli všichni zákazníci Access Control cestu migrace pro své aplikace a služby, které minimalizují množství práce potřebné k přechodu z Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webové služby, které používají aktivní ověřování

Pro webové služby, které jsou zabezpečené pomocí tokenů vydaných Access Control, Access Control nabízí následující funkce a možnosti:

- Možnost Registrovat jednu nebo více *identit služby* v oboru názvů Access Control. Identity služby lze použít k vyžádání tokenů.
- Podpora pro zabalení protokolu OAuth a protokoly OAuth 2,0 verze 13 pro vyžádání tokenů pomocí následujících typů přihlašovacích údajů:
    - Jednoduché heslo, které se vytvoří pro identitu služby
    - Podepsaný SWT pomocí symetrického klíče nebo certifikátu x509
    - Token SAML vydaný důvěryhodným poskytovatelem identity (obvykle instance AD FS)
- Podpora pro následující formáty tokenů: JWT, SAML 1,1, SAML 2,0 a SWT.
- Jednoduchá pravidla transformace tokenů.

Identity služby v Access Control se obvykle používají k implementaci ověřování mezi servery. 

#### <a name="migrate-to-azure-active-directory"></a>Migrace na Azure Active Directory

Naše doporučení pro tento typ toku ověřování je migrace na [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD je cloudový poskytovatel identity pro pracovní nebo školní účty Microsoftu. Azure AD je poskytovatel identity pro Microsoft 365, Azure a spoustu dalších věcí. 

Službu Azure AD můžete použít také k ověřování typu Server-Server pomocí implementace služby Azure AD pro udělení přihlašovacích údajů klienta OAuth. Následující tabulka porovnává možnosti Access Control v ověřování mezi servery, které jsou k dispozici ve službě Azure AD.

| Schopnost | Podpora Access Control | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| Postup registrace webové služby | Vytvoření předávající strany na portálu pro správu Access Control | Vytvoření webové aplikace Azure AD v Azure Portal |
| Registrace klienta | Vytvoření identity služby na portálu pro správu Access Control | Vytvoření další webové aplikace Azure AD v Azure Portal |
| Použitý protokol |– Protokol pro zabalení OAuth<br />– OAuth 2,0 koncept 13 udělení přihlašovacích údajů klienta | Udělení přihlašovacích údajů klienta OAuth 2.0 |
| Metody ověřování klientů |– Jednoduché heslo<br />-Podepsaná SWT<br />– Token SAML od federovaného zprostředkovatele identity |– Jednoduché heslo<br />– Podepsaný token JWT |
| Formáty tokenů |– JWT<br />– SAML 1,1<br />– SAML 2,0<br />– SWT<br /> | Pouze JWT |
| Transformace tokenů |-Přidat vlastní deklarace identity<br />-Jednoduchá deklarace IF-pak nárok vystavení | Přidat vlastní deklarace identity | 
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby správy Access Control | Podporováno pomocí rozhraní Microsoft Graph API |

Pokyny k implementaci scénářů serveru na server najdete v následujících zdrojích informací:

- Část Service-to-Service příručky pro [vývojáře Azure AD](../develop/index.yml)
- [Ukázka kódu démona pomocí jednoduchých přihlašovacích údajů klienta hesla](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Ukázka kódu démona pomocí přihlašovacích údajů klienta Certificate](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na test identity nebo Auth0

V některých případech se může stát, že přihlašovací údaje klienta Azure AD a implementace udělení OAuth nejsou dostačující k nahrazení Access Control ve vaší architektuře bez nutnosti podstatných změn kódu. Mezi běžné příklady můžou patřit:

- Ověřování mezi servery pomocí jiných formátů tokenů než JWTs.
- Ověřování mezi servery pomocí vstupního tokenu poskytnutého externím zprostředkovatelem identity.
- Ověřování mezi servery s pravidly transformace tokenu, které služba Azure AD nemůže reprodukuje.

V těchto případech můžete zvážit migraci webové aplikace do jiné cloudové ověřovací služby. Doporučujeme prozkoumat následující možnosti. Každá z následujících možností nabízí podobné funkce jako Access Control:

![Na tomto obrázku vidíte logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) je flexibilní cloudová služba identit, která [pro zákazníky Access ControlA vytvořila pokyny k migraci na vysoké úrovni](https://auth0.com/acs)a podporuje téměř všechny funkce služby ACS.

![Tento obrázek ukazuje, že se na tomto obrázku zobrazuje logo služby test identity ](./media/active-directory-acs-migration/rsz-ping.png)
 [příkazem příkazem](https://www.pingidentity.com) služby ACS. PingOne je cloudová služba identit, která podporuje mnoho stejných funkcí jako ACS a PingFederate je podobný místní produkt identity, který nabízí větší flexibilitu. Další podrobnosti o používání těchto produktů najdete v tématu [pokyny k vyřazení služby ACS](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) pomocí nástroje.

Naším cílem při práci s identitou a nástrojem Auth0 je zajistit, aby měli všichni zákazníci Access Control cestu migrace pro své aplikace a služby, které minimalizují množství práce potřebné k přechodu z Access Control.

#### <a name="passthrough-authentication"></a>Předávací ověřování

Pro průchozí ověřování pomocí libovolné transformace tokenu není k dispozici žádná ekvivalentní technologie Microsoftu pro službu ACS. Pokud to vaše zákazníci potřebují, Auth0 může být ten, který nabízí nejbližší řešení odhadu.

## <a name="questions-concerns-and-feedback"></a>Otázky, obavy a zpětná vazba

Chápeme, že mnoho Access Control zákazníkům po přečtení tohoto článku nenalezne zřetelnou cestu migrace. Je možné, že budete potřebovat pomoc nebo pokyny pro určení správného plánu. Pokud byste chtěli diskutovat na své scénáře a otázky migrace, ponechte prosím na této stránce komentář.