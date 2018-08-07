---
title: Návrh systému ochranu obsahu s více variantami DRM s ovládacím prvkem cccess pomocí Azure Media Services | Dokumentace Microsoftu
description: Další informace o tom, jak licencovat Microsoft Smooth Streaming klienta portování Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2018
ms.author: willzhan;juliako
ms.openlocfilehash: 3a33c4b157ded3162919494d27488285e52fdd3b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530842"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Návrh systému s více variantami DRM ochrany obsahu pomocí řízení přístupu 

## <a name="overview"></a>Přehled

Návrh a vytváření správy digitálních práv (DRM) subsystém pro over-the-top (OTT) nebo řešení online streamování je složitý úkol. Operátory/online video poskytovatelé obvykle externí pomocí této úlohy můžete specializované DRM poskytovatelů služeb. Cílem tohoto dokumentu je k dispozici referenční návrh a referenční implementace subsystému DRM začátku do konce v OTT nebo řešení online streamování.

Cílové čtecí zařízení pro tento dokument se techniků, kteří pracují v DRM subsystémy OTT nebo řešení online streamování/s více obrazovkami nebo čtenáře, kteří mají zájem o subsystémy DRM. Předpokladem je, že čtečky obeznámeni s alespoň jedním technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe přístup.

V této diskuzi pomocí několika variant DRM zahrnujeme 3 technologiemi DRM podporovaných službou Azure Media Services: běžné šifrování (CENC) PlayReady a Widevine, FairPlay, jakož i AES-128 s nezašifrovaným klíčem. Hlavní trendu v oboru OTT a online streamování se má používat nativní technologiemi DRM na různých klientských platformách. Tento trend je posun z předchozí, který používá jeden DRM a jeho Klientská sada SDK pro různé platformy klienta. Při použití šifrování CENC s více nativní DRM PlayReady i Widevine jsou šifrované podle [používat standard Common Encryption (CENC 23001-7 ISO/IEC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikace.

Mezi výhody používání nativní s více variantami DRM pro ochranu obsahu se, že:

* Snižuje náklady na šifrování, protože proces jedné šifrování se používá k určené pro různé platformy s jeho nativní technologiemi DRM.
* Snižuje náklady na správu prostředků, protože je potřeba jenom jednu kopii prostředku v úložišti.
* Eliminuje DRM klienta licenční poplatky, protože je nativní klient DRM obvykle zdarma na jeho nativní platformě.


### <a name="goals-of-the-article"></a>Cíle tohoto článku

Cíle tohoto článku je:

* Poskytuje referenční návrh subsystému DRM, který používá všechny 3 technologiemi DRM (CENC pro DASH), FairPlay pro HLS nebo technologie PlayReady pro technologie smooth streaming.
* Poskytuje referenční implementace na platformě Azure a Azure Media Services.
* Popisují některá témata návrh a implementace.

Následující tabulka shrnuje nativní podpora DRM na různých platformách a podpora rozšíření EME v různých prohlížečích.

| **Klientskou platformu** | **Nativní DRM** | **ROZŠÍŘENÍ EME** |
| --- | --- | --- | --- |
| **Smart TV, externích zařízení** | PlayReady, Widevine, nebo jiné | Vložený prohlížeče/EME PlayReady nebo Widevine|
| **Windows 10** | PlayReady | MS Edge/11 pro PlayReady|
| **Zařízení s androidem (telefony, tablety, TV)** |Widevine |Chrome pro Widevine |
| **iOS** | FairPlay | Safari pro FairPlay (od verze iOS 11.2) |
| **macOS** | FairPlay | Safari pro FairPlay (od verze Safari 9 + na Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Vzhledem k tomu, aktuální stav nasazení pro každou DRM obvykle služba chce implementovat dvě nebo tři technologiemi DRM, abyste měli jistotu, že je nejlepším způsobem vyřešit všechny typy koncových bodů.

Je kompromis mezi složitost logiky služby a složitostí na straně klienta k dosažení úrovně uživatelské prostředí na různých klientech.

Chcete-li váš výběr, mějte na paměti:

* PlayReady je nativně implementované v každé zařízení Windows, na některých zařízeních s Androidem a k dispozici prostřednictvím sady SDK softwaru na téměř jakoukoli platformu.
* Widevine je nativně implementovány v každé zařízení s Androidem, Chrome a některá další zařízení. Widevine je podporováno také v aplikaci Firefox a Opera přes streamování DASH.
* FairPlay je k dispozici v Iosu, macOS a tvOS.


## <a name="a-reference-design"></a>Referenční návrh
Tato část představuje referenční návrh, která je nezávislá na technologie používané na jeho implementaci.

Subsystému DRM může obsahovat následující komponenty:

* Správa klíčů
* Balení šifrování DRM
* Doručování licencí DRM
* Řízení přístupu na/Kontrola nároku
* Ověřování/autorizace uživatelů
* Aplikace přehrávače
* Počátek či content delivery network (CDN)

Následující diagram znázorňuje základní interakce mezi součástmi v subsystému DRM:

![Šifrování CENC subsystému DRM](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Návrh obsahuje tři základní vrstvy:

* Administrativní vrstvu (černá) není dostupná externě.
* Vrstva DMZ (tmavě modrá) obsahuje všechny koncové body, směřujících veřejnosti.
* Veřejné internetové vrstvy (světle modrá) obsahuje CDN a hráči s přenosy přes veřejný internet.

Je také třeba nástroj správy obsahu, který ovládací prvek ochrany DRM, bez ohledu na to, ať už jde o statické nebo dynamické šifrování. Vstupy pro šifrování DRM patří:

* MBR videoobsahem
* Klíč k obsahu
* Získání licenční adresy URL

Tady je základní tok při přehrávání:

* Ověření uživatele.
* Autorizační token se vytvoří pro uživatele.
* DRM chráněný obsah (manifest) se stáhne do přehrávač.
* Přehrávač odešle žádost o získání licence k licenčnímu serveru spolu s klíčem ID a autorizační token.

Následující část popisuje návrhu správy klíčů.

| **ContentKey asset** | **Scénář** |
| --- | --- |
| 1: 1 |Nejjednodušší případ. Poskytuje nejpodrobnější kontrolu. Ale výsledkem tohoto uspořádání obecně nejvyšší náklady doručování licencí. Minimálně jeden požadavek na licenční vyžádáním pro každý chráněný prostředek. |
| 1: n |Stejný klíč obsahu můžete použít pro více prostředků. Například pro všemi prostředky v logické skupiny, jako je například rozšířením podle tematických nebo podmnožinu žánru (nebo gene film), můžete jeden klíč k obsahu. |
| N-1 |Více klíčů obsahu jsou potřeba pro každý prostředek. <br/><br/>Například pokud je potřeba použít ochranu dynamické šifrování CENC s více technologiemi DRM pro MPEG-DASH a dynamického šifrování AES-128 pro HLS, potřebujete dva samostatné klíče obsahu. Každý klíč k obsahu potřebuje svůj vlastní ContentKeyType. (Pro klíč obsahu používají dynamické šifrování CENC ochranu použijte ContentKeyType.CommonEncryption. Pro klíč obsahu používají dynamické šifrování AES-128 použijte ContentKeyType.EnvelopeEncryption.)<br/><br/>Další příklad v CENC ochrany obsahu DASH, teoreticky vzato můžete použít jeden klíč k obsahu k ochraně datový proud videa a jiného obsahu klíč k ochraně zvukový datový proud. |
| Many-to-many |Kombinace předchozí dva scénáře. Jednu sadu klíčů obsahu se používá pro každou z více prostředků ve stejné skupině prostředků. |

Dalším důležitým faktorem, zvažte je použití trvalé a nonpersistent licence.

Tyto aspekty jsou důležité

Pokud chcete použít veřejný cloud pro naším vlastním doručováním licencí, trvalé a nonpersistent licence mít přímý vliv na náklady na licence doručování. Následující dva případy návrhů slouží pro ilustraci:

* Měsíční předplatné: použijte trvalou licenci a obsahu klíč assetu mapování 1: m. Například všech dětí filmů, používáme jeden klíč k obsahu pro šifrování. V tomto případě:

    Celkový počet licencí požadovaným pro všechny děti filmy/zařízení = 1

* Měsíční předplatné: nonpersistent licenci a mapování 1: 1 mezi klíče k obsahu a prostředků. V tomto případě:

    Celkový počet licencí požadovaným pro všechny děti filmy/zařízení = [čísla filmů sledovali vysílání televizní] x [počet relací]

Dvou různých návrzích vést k velmi odlišné licenční vzory požadavku. Různé vzorce za následek naším vlastním doručováním licencí různých Pokud veřejného cloudu, jako je například služba Media Services poskytuje službu doručování licencí.

## <a name="map-design-to-technology-for-implementation"></a>Mapa návrhu až po technologie pro implementaci
V dalším kroku obecný návrhu je namapována na technologie na platformě Azure/Media Services tak, že zadáte technologii, která se má použít pro každý stavební blok.

V následující tabulce jsou uvedeny mapování.

| **Stavebních bloků** | **Technologie** |
| --- | --- |
| **Přehrávač** |[Přehrávač médií Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Zprostředkovatele identity (IDP)** |Azure Active Directory (Azure AD) |
| **Služba tokenů zabezpečení (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Azure Media Services dynamické ochrany |
| **Doručování licencí DRM** |* Naším vlastním doručováním licencí Media Services (PlayReady, Widevine, FairPlay) <br/>* Axinom licenčního serveru <br/>* Vlastní serverové licence PlayReady |
| **Počátek** |Koncový bod streamování Azure Media Services |
| **Správa klíčů** |Nevyžaduje se pro referenční implementace |
| **Správa obsahu** |Konzolová aplikace jazyka C# |

Jinými slovy zprostředkovatele identity a služba tokenů zabezpečení jsou k dispozici ve službě Azure AD. [Rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Azure Media Services a Azure Media Player podporu CENC přes DASH, přes HLS FairPlay, PlayReady přes technologie smooth streaming a AES-128 kódování DASH, HLS nebo smooth.

Následující diagram ukazuje celkovou strukturu a tok s předchozím mapování technologie:

![Šifrování CENC v Media Services](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Nastavení ochrany obsahu DRM, správy obsahu nástroj používá následující vstupy:

* Otevřít obsah
* Klíč k obsahu z správy klíčů
* Získání licenční adresy URL
* Seznam informací ze služby Azure AD, jako je například cílové skupiny, vystavitele a tokenu deklarací identity

Zde je výstup nástroje pro správu obsahu:

* ContentKeyPolicy popisuje šablona licence DRM pro každý druh DRM používat;
* ContentKeyPolicyRestriction popisuje řízení přístupu před vydáním licence DRM
* Streamingpolicy popisuje různé kombinace formát kontejneru – režim šifrování – streamovací protokol - DRM, pro streamování
* Popisuje StreamingLocator obsahu klíč a vektor IV slouží k šifrování a adresy URL pro streamování 

Tady je tok za běhu:

* Po ověření uživatele se vygeneruje token JWT.
* Jedna z deklarací identity obsažených v tokenů JWT je deklarace skupiny, která obsahuje ID EntitledUserGroup objektu skupiny. Tato deklarace identity se používá k předání kontroly oprávnění.
* Přehrávač soubory ke stažení klienta manifestu CENC chráněný obsah a identifikuje následující:
   * ID klíče.
   * Obsah je chráněn DRM.
   * Získání adresy URL licence.
* Přehrávač díky žádost o získání licence založené na prohlížeči/DRM nepodporuje. V licenci získání žádosti o klíči, že jsou také odeslány ID a tokenů JWT. Službu doručování licencí ověří token JWT a deklarací identity obsažených předtím, než se vydá licence, které jsou potřeba.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Implementace postupů
Implementace zahrnuje následující kroky:

1. Připravte testovací prostředky. Balení/kódování videa s více přenosovými rychlostmi test fragmentovaného MP4 ve službě Media Services. Tento prostředek je *není* DRM chráněné. Ochrana DRM provádí dynamické ochrany později.

2. Vytvořte klíč ID a klíč k obsahu (volitelně z klíče počáteční hodnota). V takovém případě není nutné systémem správy klíčů, protože pouze jeden klíč ID a klíč k obsahu, které jsou požadovány pro několik prostředků testu.

3. Rozhraní API služby Media Services použijte ke konfiguraci služeb doručování licence s více variantami DRM pro testovací prostředek. Pokud používáte vlastní licenční servery ve vaší společnosti nebo vaše společnost dodavatelů místo licenční služby Media Services, můžete tento krok přeskočit. Můžete zadat licenci získání adresy URL v kroku při konfiguraci naším vlastním doručováním licencí. Rozhraní API služby Media Services je potřeba zadat některé podrobné konfigurace, jako je například omezení zásad autorizace a licence odpovědi šablony pro různé služby licence DRM. V tuto chvíli na webu Azure portal neposkytuje rozhraní potřebných pro tuto konfiguraci. Informace o rozhraní API a ukázky kódu najdete v tématu [běžného dynamického šifrování pomocí PlayReady nebo Widevine](protect-with-drm.md).

4. Konfigurace zásad doručení assetu pro testovací prostředek pomocí rozhraní API služby Media Services. Informace o rozhraní API a ukázky kódu najdete v tématu [běžného dynamického šifrování pomocí PlayReady nebo Widevine](protect-with-drm.md).

5. Vytvořit a nakonfigurovat tenanta služby Azure AD v Azure.

6. Vytvořte několik uživatelských účtů a skupin ve vašem tenantovi Azure AD. Vytvořte aspoň skupinu "Uživatele s názvem" a přidání uživatele do této skupiny. Uživatelé v této skupině se předá Kontrola nároku získání licence. Není v této skupině uživatelů k předání kontroly ověřování a nelze získat licenci. Členství v této skupině "Uživatele s názvem" je taková deklarace požadovaných skupin v JWT vydaného službě Azure AD. Tento požadavek deklarace identity v kroku zadáte při konfiguraci s více variantami DRM služeb doručování licence.

7. Vytvoření aplikace ASP.NET MVC pro hostování přehrávače videa. Tato aplikace technologie ASP.NET je chráněný pomocí ověření uživatele vůči tenanta Azure AD. Správné deklarace jsou součástí přístupové tokeny získat po ověření uživatele. Pro tento krok doporučujeme OpenID Connect rozhraní API. Nainstalujte následující balíčky NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Vytvoření s použitím přehrávače [rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) zadat technologii DRM, která má používat na různých platformách DRM.

9. V následující tabulce jsou uvedeny testovací matrice.

    | **DRM** | **Prohlížeč** | **Výsledek pro oprávnění uživatele** | **Výsledek unentitled uživatele** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge nebo Internet Explorer 11 ve Windows 10 |Úspěšné |Chyba |
    | **Widevine** |Chrome, Firefox, Opera |Úspěšné |Chyba |
    | **FairPlay** |Safari v systému macOS      |Úspěšné |Chyba |
    | **AES-128** |Většina moderních prohlížečů  |Úspěšné |Chyba |

Informace o tom, jak nastavit Azure AD pro přehrávač aplikace ASP.NET MVC naleznete v tématu [integraci aplikace využívající architekturu MVC OWIN Azure Media Services pomocí služby Azure Active Directory a omezit klíče doručování obsahu na základě deklarací identity JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Další informace najdete v tématu [ověřování tokenů JWT v Azure Media Services a dynamického šifrování](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Informace o službě Azure AD:

* Můžete najít informace pro vývojáře v [Příručka pro vývojáře Azure Active Directory](../../active-directory/develop/azure-ad-developers-guide.md).
* Můžete najít informace pro správce v [Správa tenanta adresáře Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Některé problémy v provádění
Použijte následující informace o odstraňování potíží pro pomoc při potížích s implementací.

* Vydavatel, adresa URL musí končit "/". Cílová skupina musí být ID klienta aplikace přehrávače. Přidejte také "/" na konci adresy URL vystavitele.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    V [dekodéru tokenů JWT](http://jwt.calebb.net/), uvidíte **aud** a **jednotky ISS – překročené**, jak je znázorněno token JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Přidejte oprávnění do aplikace ve službě Azure AD na **konfigurovat** kartu aplikace. Oprávnění se vyžadují pro každou aplikaci, nasazené i místní verze.

    ![Oprávnění](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Při nastavování ochrany dynamické šifrování CENC, použijte správný vystavitele.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Následující nefunguje:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identifikátor GUID je ID tenanta Azure AD. Identifikátor GUID najdete v **koncové body** rozbalovací nabídky na webu Azure Portal.

* Členství ve skupině udělit deklarací oprávnění. Ujistěte se, že je v souboru manifestu aplikace Azure AD: 

    "groupMembershipClaims": "Vše" (výchozí hodnota je null)

* Nastavte správný typ TokenType při vytváření omezení požadavků.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Vzhledem k tomu, že přidáte podporu pro token JWT (Azure AD) kromě SWT (ACS), je výchozí typ TokenType TokenType.JWT. Pokud používáte SWT/ACS, je nutné nastavit token k TokenType.SWT.

## <a name="faqs"></a>Nejčastější dotazy

Tato část popisuje některé další témata v návrhu a implementaci.

### <a name="http-or-https"></a>Protokol HTTP nebo HTTPS?
Aplikace ASP.NET MVC hráč musí podporovat následující:

* Ověřování uživatelů prostřednictvím Azure AD, která je v rámci protokolu HTTPS.
* Výměna tokenů JWT mezi klientem a Azure AD, která je v rámci protokolu HTTPS.
* Získání licence DRM klientem, který musí být v rámci protokolu HTTPS, pokud je naším vlastním doručováním licencí poskytované službou Media Services. Sada produktů PlayReady není povinné HTTPS pro naším vlastním doručováním licencí. Pokud je server licencí PlayReady mimo Media Services, můžete použít protokol HTTP nebo HTTPS.

Aplikace přehrávače ASP.NET používá protokol HTTPS jako osvědčený postup, takže Media Player je na stránce v části protokol HTTPS. HTTP je však upřednostňované pro streamování, takže je třeba vzít v úvahu problém smíšený obsah.

* Prohlížeč neumožňuje smíšený obsah. Ale mohla moduly plug-in jako Silverlight a modulu plug-in pro OSMF plynulé a pomlčka. Smíšený obsah je kvůli ohrožení schopnost injektovat škodlivý jazyka JavaScript, který může způsobit, že zákaznická data nepředáme být ohrožena zabezpečení. Prohlížeče blokovat tato funkce ve výchozím nastavení. Jediný způsob, jak obejít je na straně serveru (původní) tím, že všechny domény (bez ohledu na HTTPS nebo HTTP). To je pravděpodobně není vhodné.
* Nepoužívejte smíšený obsah. Aplikace přehrávače a Media Player by měl používat protokol HTTP nebo HTTPS. Při přehrávání smíšený obsah, technické silverlightSS zrušením upozornění smíšený obsah. Odborný flashSS zpracovává smíšený obsah bez upozornění smíšený obsah.
* Pokud váš koncový bod streamování byl vytvořen před srpnem 2014, nebudou podporovat protokol HTTPS. V tomto případě vytvořit a použít nový koncový bod streamování pro protokol HTTPS.

V referenční implementace pro chráněný pomocí DRM obsah aplikace a streamování jsou v rámci protokolu HTTPS. Pro otevřít obsah není nutné hráč ověřování nebo licenci, abyste mohli používat HTTP nebo HTTPS.

### <a name="what-is-azure-active-directory-signing-key-rollover"></a>Co je Azure Active Directory podepisování výměny klíčů?
Výměna podpisových klíčů je důležité vzít v vzít v úvahu ve vaší implementaci. Pokud ho ignorovat dokončení systém nakonec přestane fungovat zcela, do šesti týdnů nejvíce.

Azure AD využívá oborové standardy, na navázání vztahu důvěryhodnosti mezi samostatně a aplikace, které používají Azure AD. Konkrétně Azure AD používá podpisového klíče, které obsahuje pár veřejného a privátního klíče. Když Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli, je podepsaný službou Azure AD s privátním klíčem, před odesláním zpět do aplikace. Pokud chcete ověřit, že je token platný a pocházející ze ze služby Azure AD, musí aplikace ověřit podpis tokenu. Aplikace používá veřejný klíč vystavený službou Azure AD, která je součástí dokumentu federačních metadat vašeho tenanta. Tento veřejný klíč a podpisový klíč, ze kterého pochází, je stejný jako ten používá pro všechny tenanty ve službě Azure AD.

Další informace o výměně klíče služby Azure AD najdete v tématu [důležité informace o podepisování výměny klíčů ve službě Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mezi [pár veřejného a privátního klíče](https://login.microsoftonline.com/common/discovery/keys/):

* Privátní klíč ve službě Azure AD slouží ke generování token JWT.
* Veřejný klíč se používá aplikace například služeb doručování licencí DRM ve službě Media Services k ověření tokenů JWT.

Z bezpečnostních důvodů Azure AD bude otáčet certifikát pravidelně (každých šest týdnů). V případě porušení zabezpečení může dojít výměny klíčů, kdykoli. Proto služeb doručování licencí Media Services je potřeba aktualizovat veřejný klíč použít jako Azure AD otočí pár klíčů. V opačném případě se nezdaří ověření tokenu ve službě Media Services a vydává žádná licence.

K nastavení této služby nastavit TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument při konfiguraci služeb doručování licencí DRM.

Tady je token JWT toku:

* Azure AD vydá token JWT s aktuální privátní klíč pro ověřeného uživatele.
* Když hráč vidí šifrování CENC s více technologiemi DRM chráněný obsah, nejprve vyhledá token JWT vydaného službě Azure AD.
* Přehrávač odešle žádost o získání licence se token JWT služeb doručování licencí Media Services.
* Služeb doručování licencí Media Services pomocí ověření tokenů JWT před vydáním licence aktuální/platný veřejný klíč ze služby Azure AD.

Služeb doručování licencí DRM vždy zkontrolujte aktuální/platný veřejný klíč ze služby Azure AD. Veřejný klíč předložený Azure AD je klíč používaný k ověření JWT vydaného službě Azure AD.

Co když výměny klíčů se stane po Azure AD vygeneruje token JWT, ale před odesláním tokenů JWT hráči služeb doručování licencí DRM ve službě Media Services k ověření?

Protože klíč můžete provádět jednotlivě v daném okamžiku provádějí, je vždy k dispozici v dokumentu federačních metadat více než jeden platný veřejný klíč. Doručování licencí Media Services můžete použít některý z klíče specifikované v dokumentu. Protože jeden z nich může být vrácena brzy, jiné můžou být jejím nahrazení a tak dále.

### <a name="where-is-the-access-token"></a>Kde je přístupový token?
Pokud se podíváte na jak webová aplikace volá aplikaci API v rámci [identitu aplikace pomocí udělení přihlašovacích údajů klienta OAuth 2.0](../../active-directory/develop/authentication-scenarios.md#web-application-to-web-api), tok ověřování vypadá takto:

* Uživatel přihlásí ke službě Azure AD ve webové aplikaci. Další informace najdete v tématu [webového prohlížeče a webové aplikace](../../active-directory/develop/authentication-scenarios.md#web-browser-to-web-application).
* Koncový bod autorizace Azure AD přesměruje uživatelského agenta zpět do klientské aplikace s autorizační kód. Uživatelský agent vrátí autorizační kód identifikátor URI přesměrování klienta aplikace.
* Webová aplikace musí získat přístupový token, takže ho můžete ověřit do webového rozhraní API a získat požadovaný prostředek. Učiní žádost vůči koncovému bodu tokenu Azure AD a poskytuje přihlašovací údaje, ID klienta a identifikátor URI ID aplikace webového rozhraní API. Představuje autorizační kód k prokázání, že uživatel odsouhlasený.
* Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
* Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v "Autorizace" záhlaví žádosti do webového rozhraní API. Webové rozhraní API pak ověří token JWT. Pokud je ověření úspěšné, vrátí požadovaný prostředek.

V tomto toku identity aplikace webového rozhraní API důvěřuje, že webová aplikace ověřil daného uživatele. Z tohoto důvodu tento model se nazývá důvěryhodný subsystém. [Autorizace vývojový diagram](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) popisuje jak – udělení autorizačního kódu-tok funguje.

Získání licence se omezení s tokenem používá stejný vzor důvěryhodný subsystém. Službu doručování licencí Media Services je prostředek webového rozhraní API, nebo "prostředek back-end", který potřebuje přístup k webové aplikaci. Kde jsou přístupový token?

Přístupový token pochází ze služby Azure AD. Po ověření uživatele úspěšné je vrácena autorizační kód. Autorizační kód se pak použije, společně s ID klienta a klíč aplikace k výměně za přístupový token. Přístupový token se používá pro přístup k aplikaci "ukazatelů", která odkazuje na nebo představuje službu doručování licencí Media Services.

Registrace a konfigurace aplikace ukazatel ve službě Azure AD, proveďte následující kroky:

1. V tenantovi Azure AD:

   * Přidání aplikace (prostředek) pomocí adresy URL https://[resource_name].azurewebsites.net/ přihlašování. 
   * Přidáte ID aplikace pomocí adresy URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Přidejte nový klíč aplikace prostředků.

3. Aktualizujte soubor manifestu aplikace tak, aby vlastnost groupMembershipClaims má hodnotu "groupMembershipClaims": "Vše".

4. V aplikaci Azure AD, která odkazuje na webové aplikace přehrávače, v části **oprávnění k ostatním aplikacím**, přidání aplikace prostředku, který byl přidán v kroku 1. V části **delegovaná oprávnění**vyberte **přístupu [resource_name]**. Tato možnost poskytuje oprávnění webové aplikace k vytvoření přístupové tokeny, které přístup k aplikaci prostředků. Proveďte pro místní a nasazené verze webové aplikace při vývoji v sadě Visual Studio a webové aplikace Azure.

Token JWT vydaného službě Azure AD je přístupový token pro přístup k prostředku ukazatele.

### <a name="what-about-live-streaming"></a>A co živé streamování?
Předchozí diskuze, zaměřuje na prostředky na vyžádání. A co živé streamování?

K ochraně živého streamování služby Media Services díky tomu asset přidružený k programu jako asset videa na Vyžádání, že můžete přesně stejné návrh a implementaci.

Konkrétně jak živě Streamovat ve službě Media Services, musíte vytvořit kanál a pak vytvoření programu v rámci kanálu. K vytvoření programu, potřebujete pouze vytvořit asset, který obsahuje živý archív programu. Šifrování CENC s více variantami DRM ochrany živého obsahu, aby stejné nastavení/zpracování na asset použijte jako by šlo asset videa na Vyžádání před spuštěním programu.

### <a name="what-about-license-servers-outside-media-services"></a>A co licenční servery mimo Media Services?

Zákazníci často investovali do serverové farmy licence buď ve své vlastní datové centrum nebo jeden hostitelských počítačích poskytovatelů služeb DRM. Pomocí Media Services content protection můžete provozovat v hybridním režimu. Obsah můžete hostované a dynamicky chráněné ve službě Media Services, zatímco licence DRM jsou poskytované servery mimo Media Services. V takovém případě zvažte následující změny:

* Služba tokenů zabezpečení je potřeba vystavovat tokeny, které jsou přijatelné a dá ověřit pomocí licence serverové farmy. Servery licence Widevine poskytované Axinom například vyžadovat konkrétní token JWT, který obsahuje zprávu o oprávnění. Proto budete muset mít služby STS pro vydávání takový token JWT. 
* Už je potřeba nakonfigurovat službu doručování licencí Media Services. Je potřeba zadat licence získání adresy URL (PlayReady, Widevine a FairPlay) při konfiguraci ContentKeyPolicies.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co když chci používat vlastní službu STS?
Zákazník může rozhodnout použít vlastní službu STS poskytnout tokeny Jwt. Mezi důvody patří:

* Zprostředkovatel identity využívaných zákazníkem nepodporuje službu tokenů zabezpečení. V takovém případě vlastních služeb STS, může být možnost.
* Zákazník může být nutné flexibilní nebo užší ovládací prvek integrovat službu tokenů zabezpečení zákazníka odběratele fakturačním systémem. Například operátor MVPD mohou nabízet více balíčků OTT odběratele, jako je například premium, basic nebo sportu. Operátor může být vhodné tak, aby odpovídaly deklarací identity v tokenu s balíčkem předplatitele, tak, že jsou k dispozici pouze obsah v určitém balíčku. V takovém případě vlastních služeb STS nabízí potřebné flexibility a kontroly.

Při použití vlastních služeb STS musí udělali dvě změny:

* Když nakonfigurujete službu doručování licencí pro určitý prostředek, musíte zadat klíč zabezpečení použijí k ověření pomocí vlastních služeb STS místo platnost aktuálního klíče ze služby Azure AD. (Další podrobnosti podle.) 
* Při vygenerování tokenu JTW klíč zabezpečení je zadán místo aktuální X509 privátní klíč certifikátu ve službě Azure AD.

Existují dva typy bezpečnostních klíčů:

* Symetrický klíč: stejný klíč se používá ke generování a ověřit token JWT.
* Asymetrický klíč: pár veřejného a privátního klíče v x X509 certifikát se používá s privátním klíčem k šifrování a generovat token JWT a s veřejným klíčem k ověření tokenu.

> [!NOTE]
> Pokud používáte rozhraní .NET Framework a jazyka C# jako svou vývojovou platformu, X509 certifikát používaný pro asymetrické bezpečnostní klíč musí mít klíč délku aspoň 2048. Jde o požadavek třídy System.IdentityModel.Tokens.X509AsymmetricSecurityKey v rozhraní .NET Framework. V opačném případě je vyvolána následující výjimka:

> IDX10630: Nesmí být menší než "2048" bits "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" pro podepisování.

## <a name="the-completed-system-and-test"></a>Dokončené systému a testování
Tato část vás provede následující scénáře v dokončené začátku do konce systému tak, aby základní přehled o chování může mít, než se ponoříte přihlašovací účet:

* Pokud potřebujete neintegrovaném scénáře:

    * Pro video-prostředků ve službě Media Services, které jsou hostované buď nechráněné nebo chráněné DRM, ale bez ověřování pomocí tokenu (vystavování licenci těmi požadovaná), takže ji můžete otestovat bez přihlášení. Přepnout na HTTP, pokud vaše video datové proudy přes protokol HTTP.

* Pokud je třeba integrovaný scénář začátku do konce:

    * Pro video-prostředků v rámci dynamických DRM ochrany ve službě Media Services pomocí ověření tokenu a JWT vygenerované službou Azure AD budete muset přihlásit.

Webové aplikace přehrávače a jeho přihlášení najdete v tématu [tento web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Přihlášení uživatele
K otestování integrovaného systému DRM začátku do konce, musíte mít účet vytvoření nebo přidání.

Který účet?

I když Azure původně umožňovala přístup pouze uživatelům účtů Microsoft, je nyní povolen přístup uživatelům z obou systémů. Všechny vlastnosti v Azure teď vztah důvěryhodnosti Azure AD pro ověřování a Azure AD ověřuje organizační uživatele. Vztah federace byl vytvořen, kde Azure AD nechává systémem identit uživatelů účtů Microsoft spotřebitelské uživatele ověřovat. V důsledku toho může Azure AD ověřit hostovaný Microsoft Azure AD stejně jako nativní účty.

Protože Azure AD nechává doména účtu Microsoft, můžete přidat všechny účty v kterékoli z následujících domén do vlastní služby Azure AD tenanta a přihlaste se pomocí účtu:

| **Název domény** | **Domény** |
| --- | --- |
| **Doména tenanta vlastní služby Azure AD** |somename.onmicrosoft.com |
| **Podnikové doméně** |microsoft.com |
| **Doména účtu Microsoft** |Outlook.com, live.com, hotmail.com |

Autoři měli účet vytvoření nebo přidání za vás může kontaktovat.

Na následujících snímcích obrazovky zobrazit různé přihlašovací stránky používá jinou doménu účty:

**Vlastní služby Azure AD tenant účet domény**: přizpůsobené přihlašovací stránky z vlastní služby Azure AD tenanta domény.

![Účtu tenanta domény vlastní služby Azure AD](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft doménový účet s čipovou kartu**: přihlašovací stránky přizpůsobený microsoftem podnikové IT s dvoufaktorovým ověřováním.

![Účtu tenanta domény vlastní služby Azure AD](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: přihlašovací stránku účtu Microsoft pro zákazníky.

![Účtu tenanta domény vlastní služby Azure AD](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití rozšíření šifrované média pro PlayReady
Plán PlayReady je v moderním prohlížeči s Media rozšíření eme (Encrypted Extensions) pro podporu PlayReady, jako je například Internet Explorer 11 na Windows 8.1 nebo novější a prohlížeč Microsoft Edge ve Windows 10 v podkladové DRM pro rozšíření EME.

![Použití rozšíření EME PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Tmavě player oblasti totiž ochranou PlayReady brání v provádění snímek obrazovky videa chráněné.

Následující snímek obrazovky ukazuje player moduly plug-in a Microsoft Security Essentials (MSE) / EME podporují:

![Přehrávač moduly plug-in pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Rozšíření EME v Microsoft Edge a Internet Explorer 11 na Windows 10 umožňuje [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) má být volána na zařízeních s Windows 10, které ho podporují. PlayReady SL3000 odemkne tok obsahu rozšířené úrovně premium (4 kB, HDR) a nový obsah doručování modely (pro rozšířený obsah).

PlayReady a zaměřte se na zařízení Windows, je pouze DRM v části hardware, které jsou k dispozici na zařízeních s Windows (PlayReady SL3000). Služba streamování pomocí PlayReady přes EME nebo pomocí aplikace pro univerzální platformu Windows a nabízejí vyšší kvalitu videa pomocí technologie PlayReady SL3000 než jiné DRM. Obvykle obsahu až do 2 tis. toků prostřednictvím prohlížeče Chrome nebo Firefox a obsahu až 4 tis. toků prostřednictvím Microsoft Edge nebo Internet Explorer 11 nebo aplikace pro univerzální platformu Windows na jednom zařízení. Závisí na nastavení služby a implementaci.

#### <a name="use-eme-for-widevine"></a>Použití rozšíření EME Widevine
V moderním prohlížeči s podporou EME/Widevine, jako je Chrome 41 + na Windows 10, Windows 8.1, Mac OS x Yosemite nebo Chrome na Android bodu 4.4.4 je Google Widevine DRM za EME.

![Použití rozšíření EME Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nezabrání provádění snímek obrazovky videa chráněné.

![Přehrávač moduly plug-in pro Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Použití rozšíření EME pro FairPlay
Podobně můžete otestovat FairPlay chráněný obsah v této testovací player v prohlížeči Safari v systému macOS nebo iOS 11.2 a novější.

Ujistěte se, že zadáte "FairPlay" jako protectionInfo.type a umístit do správné adresy URL pro certifikát vaší aplikace v cestě AC snímků za Sekundu (FairPlay Streaming aplikace cesta k certifikátu).

### <a name="unentitled-users"></a>Unentitled uživatelů
Pokud uživatel není členem skupiny "Uživatelů s názvem", uživatel neprojde kontrola oprávnění. Licenční služby s více variantami DRM odmítá pak vydá požadovanou licenci, jak je znázorněno. Podrobný popis je "získat licenci se nepovedlo," což je tak, jak navrženo.

![Unentitled uživatelů](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění vlastní bezpečnostní služby tokenu
Při spuštění vlastních služeb STS, tokenů JWT je vydané vlastní službu STS s použitím symetrický nebo asymetrickým klíčem.

Na následujícím snímku obrazovky představuje scénář, který používá symetrického klíče (s použitím Chrome):

![Vlastní službu STS s symetrický klíč](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který se používá asymetrické klíče prostřednictvím x X509 certifikát (s použitím Microsoft moderním prohlížeči):

![Vlastní službu STS s asymetrický klíč](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Ověřování uživatelů v obou předchozích případech zůstává stejný. To se provádí prostřednictvím služby Azure AD. Jediným rozdílem je, že jsou vydané tokeny Jwt vlastních služeb STS místo Azure AD. Když nakonfigurujete dynamické šifrování CENC ochranu, licenční omezení doručování služby určuje typ tokenů JWT symetrický nebo asymetrickým klíčem.

## <a name="summary"></a>Souhrn
Tento dokument popsány ochrany obsahu pomocí 3 technologiemi DRM a řízením přístupu prostřednictvím ověřování pomocí tokenu, návrhem a její implementaci pomocí Azure, Azure Media Services a Azure Media Playeru.

* Referenční návrh byl vystaven, který obsahuje všechny potřebné komponenty v subsystému DRM.
* Referenční implementace se zobrazí na Azure, Azure Media Services a Azure Media Playeru.
* Některá témata, která je přímo součástí návrhu a implementace byly také popsány.

## <a name="next-steps"></a>Další postup

[Chránit obsah pomocí DRM](protect-with-drm.md)
