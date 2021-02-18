---
title: Systém ochrany obsahu s více technologiemi DRM
description: V tomto článku najdete podrobný popis návrhu systému ochrany obsahu s více technologiemi DRM pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: bb43396260e1b914f9f5b9e9ae489e87042a3154
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092432"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Vytvoření a vytvoření subsystému digitálního Rights Management (DRM) pro řešení OTT nebo online streamování je složitý úkol. Operátoři a online poskytovatelé videí obvykle používají jako externí tuto úlohu pro specializované poskytovatele služeb DRM. Cílem tohoto dokumentu je předložit referenční návrh a implementaci referenční implementace komplexního subsystému DRM v řešení OTT nebo online streamování.

Cíloví čtenáři tohoto dokumentu jsou vývojáři, kteří pracují v subsystému DRM OTT nebo online řešení pro streamování a na více obrazovkách nebo čtenářích, kteří mají zájem o subsystémy DRM. Předpokladem je, že čtenáři jsou obeznámené s aspoň jednou z technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe Access.

V této diskuzi se službou multi-DRM nabízíme 3 několikanásobnou, které podporuje Azure Media Services: Common Encryption (CENC) pro PlayReady a Widevine, FairPlay a AES-128 vymazat šifrování klíčů. Hlavním trendem v online streamování a OTTm odvětví je použití nativních několikanásobnou na různých klientských platformách. Tento trend je posun od předchozí verze, která používala jednu DRM a její klientskou sadu SDK pro různé klientské platformy. Pokud používáte CENC s více nativními technologiemi DRM, jsou protokoly PlayReady i Widevine šifrované podle specifikace [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Výhodou použití nativního využívání více DRM pro ochranu obsahu je to, že:

* Snižuje náklady na šifrování, protože jeden proces šifrování se používá k zacílení různých platforem na nativní několikanásobnou.
* Snižuje náklady na správu prostředků, protože v úložišti je potřeba jenom jedna kopie assetu.
* Eliminuje náklady na licencování klientů DRM, protože nativní klient DRM je obvykle zdarma na své nativní platformě.

### <a name="goals-of-the-article"></a>Cíle článku

Cílem tohoto článku je:

* Poskytněte referenční návrh subsystému DRM, který používá všechny 3 několikanásobnou (CENC pro POMLČKu, FairPlay pro HLS a PlayReady pro hladké streamování).
* Poskytněte referenční implementaci na platformě Azure a Azure Media Services.
* Prodiskutujte témata týkající se návrhu a implementace.

Následující tabulka shrnuje nativní podporu DRM na různých platformách a podporu EME v různých prohlížečích.

| **Platforma klienta** | **Nativní DRM** | **EME** |
| --- | --- | --- |
| **Inteligentní televizory, STBs** | PlayReady, Widevine a/nebo jiné | Vložený prohlížeč/EME pro PlayReady a/nebo Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 pro PlayReady|
| **Zařízení s Androidem (telefon, tablet, TV)** |Widevine |Chrome pro Widevine |
| **iOS** | FairPlay | Safari pro FairPlay (od verze iOS 11,2) |
| **macOS** | FairPlay | Safari pro FairPlay (vzhledem k tomu, že Safari 9 + v Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Vzhledem k tomu, že aktuální stav nasazení pro každou DRM, služba obvykle vyžaduje implementaci dvou nebo tří několikanásobnou, aby bylo zajištěno, že budete řešit všechny typy koncových bodů tak, aby byly nejlepším způsobem.

Existuje kompromis mezi složitostí logiky služby a složitostí na straně klienta, aby se dosáhlo určité úrovně uživatelského prostředí na různých klientech.

Udělejte si výběr:

* Technologie PlayReady je nativně implementovaná v každém zařízení s Windows, na některých zařízeních s Androidem a dostupná prostřednictvím softwarových sad SDK na prakticky libovolné platformě.
* Widevine se nativně implementuje na všech zařízeních s Androidem, v Chrome a v některých dalších zařízeních. Widevine je také podporován v prohlížečích Firefox a Operau nad POMLČKou.
* FairPlay je k dispozici v iOS, macOS a tvOS.

## <a name="a-reference-design"></a>Referenční návrh

V této části najdete referenční návrh, který se nezávislá na technologie používané k jeho implementaci.

Subsystém DRM může obsahovat následující součásti:

* Správa klíčů
* Balíček šifrování DRM
* Doručování licencí DRM
* Kontrola oprávnění/řízení přístupu
* Ověřování uživatelů/autorizace
* Aplikace přehrávače
* Původ/Content Delivery Network (CDN)

Následující diagram znázorňuje interakci vysoké úrovně mezi součástmi v subsystému DRM:

![Subsystém DRM s CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Návrh má tři základní vrstvy:

* Vrstva Back-Office (černá) není vystavená externě.
* DMZ vrstva (tmavě modrá) obsahuje všechny koncové body, které čelí veřejnému.
* Veřejná internetová vrstva (světle modrá) obsahuje CDN a přehrávače s přenosy přes veřejný Internet.

Také by měl být k dispozici nástroj pro správu obsahu k řízení ochrany DRM bez ohledu na to, zda se jedná o statické nebo dynamické šifrování. Mezi vstupy pro šifrování DRM patří:

* Obsah videa MBR
* Klíč obsahu
* Adresy URL pro získání licence

Tady je tok vysoké úrovně během doby přehrávání:

* Uživatel je ověřený.
* Pro uživatele se vytvoří autorizační token.
* Do přehrávače se stáhne obsah chráněný přes DRM (manifest).
* Přehrávač odesílá žádosti o získání licence na licenční servery spolu s ID klíče a autorizačním tokenem.

V následující části je popsána návrh správy klíčů.

| **ContentKey na Asset** | **Scénář** |
| --- | --- |
| 1 – 1 |Nejjednodušší případ. Poskytuje ovládací prvek nejlepší. Toto uspořádání obecně má za následek nejvyšší náklady na doručení licence. U každého chráněného assetu se vyžaduje minimálně jedna žádost o licenci. |
| 1 – mnoho |Můžete použít stejný klíč obsahu pro více prostředků. Například pro všechny prostředky v logické skupině, jako je například Žánr nebo podmnožina žánru (nebo datové genu), můžete použít jediný klíč obsahu. |
| M:n – 1 |Pro každý Asset je potřeba více klíčů obsahu. <br/><br/>Pokud například potřebujete použít dynamickou ochranu CENC s více technologiemi DRM pro HLS a dynamické šifrování AES-128 pro, budete potřebovat dva samostatné klíče obsahu. Každý klíč obsahu potřebuje vlastní ContentKeyType. (Pro klíč obsahu, který se používá pro dynamickou CENC Protection, použijte ContentKeyType. CommonEncryption. Pro klíč obsahu, který se používá pro dynamické šifrování AES-128, použijte ContentKeyType. EnvelopeEncryption.)<br/><br/>Dalším příkladem je, že v CENC ochraně PŘERUŠOVANého obsahu je možné pomocí jednoho klíče obsahu chránit datový proud videa a další klíč obsahu a chránit tak zvukový datový proud. |
| Relace několika k několika jiným |Kombinace předchozích dvou scénářů. Jedna sada klíčů obsahu se používá pro každý z více prostředků ve stejné skupině prostředků. |

Dalším důležitým faktorem, který je třeba zvážit, je použití trvalých a netrvalých licencí.

Proč jsou tyto důležité informace důležité?

Pokud používáte veřejný cloud pro doručování licencí, trvalá a netrvalá licence mají přímý dopad na náklady na doručování licencí. Následující dva různé případy návrhu slouží k ilustraci:

* Měsíční předplatné: použijte trvalou licenci a mapování klíč-to-Asset pro klíč k obsahu 1: n. Například pro všechny filmové děti používáme k šifrování jeden klíč obsahu. V tomto případě:

    Celkový počet licencí požadovaných pro všechny své filmové filmy/zařízení = 1

* Měsíční předplatné: použijte netrvalou licenci a mapování 1:1 mezi klíčem obsahu a Assetem. V tomto případě:

    Celkový počet licencí požadovaných pro všechny služby Kids video/zařízení = [počet sledovaných filmů] x [počet relací]

Výsledkem dvou různých návrhů je velmi odlišný vzor požadavků na licence. Výsledkem různých vzorů je náklady na doručování licencí, pokud je služba doručování licencí poskytována veřejným cloudem, například Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mapování návrhu na technologii pro implementaci
V dalším kroku je obecný návrh namapován na technologie na platformě Azure nebo Media Services určením technologie, která se má použít pro jednotlivé stavební bloky.

Mapování jsou uvedena v následující tabulce.

| **Stavební blok** | **Technologie** |
| --- | --- |
| **Player** |[Přehrávač médií Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Zprostředkovatel identity (IDP)** |Azure Active Directory (Azure AD) |
| **Služba tokenů zabezpečení (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Azure Media Services dynamickou ochranu |
| **Doručování licencí DRM** |* Media Services doručování licencí (PlayReady, Widevine, FairPlay) <br/>* Licenční server Axinom <br/>* Vlastní licenční server PlayReady |
| **Zdroji** |Koncový bod streamování Azure Media Services |
| **Správa klíčů** |Není nutné pro implementaci reference. |
| **Správa obsahu** |Konzolová aplikace v jazyce C# |

Jinými slovy, služba Azure AD poskytuje IDP i STS. [Rozhraní API pro Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Azure Media Services i Azure Media Player podporují CENC přes POMLČKu, FairPlay nad HLS, PlayReady přes hladké streamování a šifrování AES-128 pro POMLČKy, HLS a hladké.

Následující diagram ukazuje celkovou strukturu a tok s předchozím mapováním technologie:

![CENC na Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Pro nastavení ochrany obsahu DRM používá nástroj pro správu obsahu následující vstupy:

* Otevření obsahu
* Klíč obsahu ze správy klíčů
* Adresy URL pro získání licence
* Seznam informací z Azure AD, jako je cílová skupina, Vystavitel a deklarace tokenů

Tady je výstup nástroje pro správu obsahu:

* ContentKeyPolicy popisuje šablonu licence DRM pro každý druh používaného DRM.
* ContentKeyPolicyRestriction popisuje řízení přístupu před vystavením licence DRM.
* Streamingpolicy popisuje různé kombinace standardu DRM – formát pro streamování protokolů-streaming Protocol-Container pro streamování
* StreamingLocator popisuje klíč obsahu/IV, který se používá pro šifrování, a adresy URL pro streamování. 

Toto je tok za běhu:

* Po ověření uživatele se vygeneruje token JWT.
* Jedna z deklarací identity obsažených v tokenu JWT je deklarace skupiny, která obsahuje ID objektu skupiny EntitledUserGroup. Tato deklarace identity se používá k předání kontroly nároků.
* Přehrávač stáhne manifest klienta obsahu chráněný CENC a identifikuje následující:
   * ID klíče
   * Obsah je chráněn technologií DRM.
   * Adresy URL pro získání licence.
* Přehrávač vytvoří žádost o získání licence na základě podporovaného prohlížeče/DRM. V žádosti o získání licence se také odesílají ID klíče a token JWT. Služba doručování licencí ověří token JWT a deklarace, které obsahovaly, před tím, než vystaví potřebné licence.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Postupy implementace
Implementace zahrnuje následující kroky:

1. Připravte prostředky testu. Zakódování nebo zabalení testovacího videa pro fragmentaci MP4 s více přenosovými rychlostmi v Media Services. Tento *prostředek není chráněn technologií DRM.* Ochrana DRM se provádí později dynamickou ochranou.

2. Vytvořte ID klíče a klíč obsahu (volitelně od počátečního klíče). V této instanci není potřeba systém správy klíčů, protože pro několik testovacích prostředků je potřeba jenom jedno ID klíče a klíč obsahu.

3. Pomocí rozhraní Media Services API můžete nakonfigurovat služby pro doručování licencí s více technologiemi DRM pro testovací prostředek. Pokud používáte vlastní licenční servery vaší společnosti nebo dodavatelů vaší společnosti namísto licenčních služeb v Media Services, můžete tento krok přeskočit. V kroku můžete zadat adresy URL pro získání licence, když nakonfigurujete doručování licencí. Rozhraní Media Services API je potřeba k určení některých podrobných konfigurací, jako jsou například omezení zásad autorizace a šablony odpovědí na licence pro různé licenční služby DRM. V tuto chvíli Azure Portal neposkytuje potřebné uživatelské rozhraní pro tuto konfiguraci. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](protect-with-drm.md).

4. Použijte rozhraní Media Services API ke konfiguraci zásad doručení assetu pro testovací prostředek. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](protect-with-drm.md).

5. Vytvořte a nakonfigurujte tenanta Azure AD v Azure.

6. Vytvořte v tenantovi Azure AD několik uživatelských účtů a skupin. Vytvořte alespoň skupinu "oprávněný uživatel" a přidejte uživatele do této skupiny. Uživatelé v této skupině předají kontrolu nároků v získání licence. Uživatelům, kteří nejsou v této skupině, se nepodaří předat kontrolu ověřování a nemůže získat licenci. Členství v této skupině "nazvaný uživatel" je požadovanou deklarací skupin v tokenu JWT vydaném službou Azure AD. Tento požadavek na deklaraci identity zadáte v kroku při konfiguraci služby pro doručování licencí s více technologiemi DRM.

7. Vytvořte aplikaci ASP.NET MVC pro hostování vašeho přehrávače videa. Tato aplikace ASP.NET je chráněná ověřováním uživatelů v tenantovi Azure AD. Do přístupových tokenů získaných po ověření uživatele jsou zahrnuty správné deklarace identity. Pro tento krok doporučujeme rozhraní API pro OpenID Connect. Nainstalujte následující balíčky NuGet:

   * Install-Package Microsoft. Azure. Active. GraphClient
   * Install-Package Microsoft. Owin. Security. OpenIdConnect
   * Install-Package Microsoft. Owin. Security. cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft. IdentityModel. clients. dataactive

8. Vytvořte přehrávač pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, která technologie DRM se má používat na různých platformách DRM.

9. V následující tabulce je uvedena testovací matice.

    | **DIGITÁLNÍCH** | **Prohlížeč** | **Výsledek pro oprávněného uživatele** | **Výsledek pro neoprávněného uživatele** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge nebo Internet Explorer 11 ve Windows 10 |Provedena |Neúspěch |
    | **Widevine** |Chrome, Firefox, Opera |Provedena |Neúspěch |
    | **FairPlay** |Safari v macOS      |Provedena |Neúspěch |
    | **AES-128** |Většina moderních prohlížečů  |Provedena |Neúspěch |

Informace o tom, jak nastavit Azure AD pro aplikaci aktéra ASP.NET MVC, najdete v tématu [integrace aplikace založené na Azure Media Services Owin MVC s Azure Active Directory a omezení doručování klíčů obsahu na základě DEKLARACÍ JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Další informace najdete v tématu [ověřování tokenu JWT v Azure Media Services a dynamické šifrování](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Informace o službě Azure AD:

* Informace o vývojářích najdete v [příručce pro vývojáře Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informace o Správci najdete ve [správě adresáře tenanta Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Některé problémy v implementaci

Pro pomoc s problémy s implementací použijte následující informace pro řešení potíží.

* Adresa URL vystavitele musí končit znakem "/". Cílovou skupinou musí být ID klienta aplikace aktéra. Na konec adresy URL vystavitele také přidejte znak "/".

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    V [dekodéru JWT](http://jwt.calebb.net/)vidíte **AUD** a **ISS**, jak je znázorněno v tokenu JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Přidejte do aplikace ve službě Azure AD oprávnění na kartě **Konfigurace** aplikace. Pro každou aplikaci jsou vyžadována oprávnění, místní i nasazená verze.

    ![Oprávnění](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Při nastavování dynamické ochrany CENC použijte správný Vystavitel.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Následující nefunguje:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    Identifikátor GUID je ID tenanta Azure AD. Identifikátor GUID lze nalézt v místní nabídce **koncových bodů** v Azure Portal.

* Udělte oprávnění deklarací identity členství ve skupině. Ujistěte se, že je v souboru manifestu aplikace služby Azure AD následující: 

    "groupMembershipClaims": "All" (výchozí hodnota je null)

* Nastavte správné typ TokenType při vytváření požadavků na omezení.

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Protože kromě služby SWT (ACS) přidáte podporu pro token JWT (Azure AD), výchozí typ TokenType je typ TokenType. JWT. Pokud používáte SWT/ACS, musíte nastavit token na typ TokenType. SWT.

## <a name="the-completed-system-and-test"></a>Dokončený systém a test

Tato část vás provede následujícími scénáři v dokončeném kompletním systému, abyste mohli získat základní přehled o chování před tím, než se přihlásíte k přihlašovacímu účtu:

* Pokud potřebujete scénář, který není integrovaný:

    * U assetů, které jsou hostovány v Media Services, které jsou buď nechráněný, nebo chráněná technologií DRM, ale bez ověřování tokenu (vydání licence pro vás, kdo si ji vyžádal), je můžete otestovat bez přihlášení. Pokud streamování videa používá protokol HTTP, přepněte na HTTP.

* Pokud potřebujete kompletní integrovaný scénář:

    * U prostředků videa v části dynamická ochrana DRM v Media Services se musíte přihlásit pomocí tokenu ověřování a JWT vygenerovaného službou Azure AD.

Pro webovou aplikaci Player a její přihlášení si přečtěte [Tento web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Přihlášení uživatele
Aby bylo možné otestovat kompletní integrovaný systém DRM, je nutné vytvořit nebo přidat účet.

Jaký účet?

I když Azure byl původně povolený jenom pomocí účet Microsoft uživatelů, přístup je teď povolený pro uživatele z obou systémů. Všechny vlastnosti Azure teď pro ověřování důvěřují Azure AD a Azure AD ověřuje uživatele organizace. Byl vytvořen federační vztah, ve kterém služba Azure AD důvěřuje účet Microsoft systém identit spotřebitelů k ověřování uživatelů uživatele. V důsledku toho může Azure AD ověřovat účty Microsoft hosta i nativní účty Azure AD.

Vzhledem k tomu, že Azure AD důvěřuje účet Microsoft doméně, můžete do vlastního tenanta Azure AD přidat libovolné účty z následujících domén a použít účet pro přihlášení:

| **Název domény** | **Doména** |
| --- | --- |
| **Vlastní doména tenanta Azure AD** |somename.onmicrosoft.com |
| **Firemní doména** |microsoft.com |
| **Doména účet Microsoft** |outlook.com, live.com, hotmail.com |

Můžete kontaktovat libovolného autora, aby byl účet vytvořený nebo přidaný za vás.

Následující snímky obrazovky zobrazují různé přihlašovací stránky používané různými účty domény:

**Vlastní účet domény tenanta Azure AD**: přizpůsobená přihlašovací stránka vlastní domény TENANTA Azure AD.

![Vlastní účet domény tenanta Azure AD One](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Účet domény Microsoft s čipovou kartou**: přihlašovací stránka přizpůsobená MICROSOFTem IT má dvojúrovňové ověřování.

![Vlastní účet domény klienta Azure AD 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: přihlašovací stránka účet Microsoft pro příjemce.

![Vlastní účet domény tenanta Azure AD – tři](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití šifrovaných rozšíření médií pro PlayReady

V moderním prohlížeči s rozšířeními EME (Encrypted Media Extensions) pro podporu PlayReady, jako je Internet Explorer 11 v Windows 8.1 nebo novějším a v prohlížeči Microsoft Edge ve Windows 10, je PlayReady základní DRM pro EME.

![Použití EME pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Tmavá oblast přehrávače je, protože ochrana PlayReady zabraňuje tomu, aby se snímek obrazovky s chráněným videem zabránil.

Následující snímek obrazovky ukazuje moduly plug-in přehrávače a podpora programu Microsoft Security Essentials (MSE)/EME:

![Moduly plug-in přehrávače pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME na Microsoft Edge a Internet Explorer 11 ve Windows 10 umožňuje, aby se [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) vyvolal na zařízeních s Windows 10, která ho podporují. PlayReady SL3000 odemkne tok vylepšeného obsahu Premium (4K, HDR) a nové modely doručování obsahu (pro lepší obsah).

V zařízeních s Windows se můžete soustředit jenom na hardware, který je k dispozici na zařízeních s Windows (PlayReady SL3000). Služba streamování může používat PlayReady prostřednictvím EME nebo prostřednictvím aplikace Univerzální platforma Windows a nabízí vyšší kvalitu videa pomocí technologie PlayReady SL3000 než jiné DRM. Obvykle se obsah až do 2K toků prostřednictvím Chrome nebo Firefox a obsah až do 4K toků přes Microsoft Edge/Internet Explorer 11 nebo aplikace Univerzální platforma Windows na stejném zařízení. Tato hodnota závisí na nastavení a implementaci služby.

#### <a name="use-eme-for-widevine"></a>Použití EME pro Widevine

V moderním prohlížeči s podporou EME/Widevine, jako je například Chrome 41 + ve Windows 10, Windows 8.1, Mac OSX Yosemite a Chrome v Androidu 4.4.4, je Google Widevine je technologie DRM za EME.

![Použití EME pro Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nebrání v vytváření snímku obrazovky chráněného videa.

![Moduly plug-in přehrávače pro Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Použití EME pro FairPlay

Podobně můžete testovat obsah chráněný FairPlay v tomto testovacím přehrávači v Safari na macOS nebo iOS 11,2 a novějším.

Ujistěte se, že jste zadali "FairPlay" jako protectionInfo. Zadejte a vložte do správné adresy URL pro svůj certifikát aplikace v cestě STŘÍDAVého proudu snímků (cesta k certifikátu aplikace FairPlay streaming).

### <a name="unentitled-users"></a>Neoprávnění uživatelé

Pokud uživatel není členem skupiny "oprávnění uživatelé", uživatel neprojde kontrolu nároků. Licenční služba s více technologiemi DRM pak odmítne vystavit požadovanou licenci, jak je znázorněno na obrázku. Podrobný popis je "získání licence se nezdařilo", což je navrženo.

![Neoprávnění uživatelé](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění vlastní služby tokenu zabezpečení

Pokud spustíte vlastní STS, vystaví se token JWT vlastní službou STS pomocí symetrického nebo asymetrického klíče.

Následující snímek obrazovky ukazuje scénář, který používá symetrický klíč (pomocí Chromu):

![Vlastní STS s symetrickým klíčem](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který používá asymetrický klíč prostřednictvím certifikátu x509 (pomocí moderního prohlížeče Microsoftu):

![Vlastní STS s asymetrickým klíčem](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

V obou předchozích případech zůstává ověřování uživatelů stejné. Probíhá přes Azure AD. Jediným rozdílem je, že JWTs vydávají vlastní STS místo Azure AD. Když konfigurujete dynamickou ochranu CENC Protection, omezení služby doručování licencí určuje typ JWT, buď symetrický, nebo asymetrický klíč.

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy](frequently-asked-questions.md)
* [Přehled ochrany obsahu](content-protection-overview.md)
* [Chraňte svůj obsah pomocí DRM](protect-with-drm.md)
