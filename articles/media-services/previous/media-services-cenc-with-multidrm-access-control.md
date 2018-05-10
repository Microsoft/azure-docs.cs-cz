---
title: 'Šifrování CENC s více technologiemi DRM a řízení přístupu: referenční návrhu a implementace v Azure a Azure Media Services | Microsoft Docs'
description: Další informace o tom, jak licencovat Microsoft technologie Smooth Streaming klienta portování Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: cfowler
editor: ''
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: 8f072f13909190eee194565673ccfa1f381f7503
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>Šifrování CENC s více technologiemi DRM a řízení přístupu: referenční návrhu a implementace v Azure a Azure Media Services
 
## <a name="introduction"></a>Úvod
Navrhování a vytváření podsystému digitální rights management (DRM) pro over-the horní (OTT) nebo řešení online datového proudu je složité úlohy. Operátory, online poskytovatelů videa se externí obvykle tuto úlohu na specializované DRM poskytovatele služeb. Cílem tohoto dokumentu je poskytnout odkaz návrhu a implementace podsystému začátku do konce DRM v OTT nebo streamování řešení online.

Cílové čtečky pro tento dokument se technici, kteří pracují v DRM subsystémy OTT nebo streamování Multiobrazovka řešení online nebo čtenáři, kteří mají zájem o subsystémy DRM. Předpokladem je, že čtečky obeznámeni s alespoň jedním z technologiemi DRM na trhu, např. PlayReady, Widevine, FairPlay nebo Adobe Access.

V tomto výkladu DRM jsme také obsahovat běžné šifrování (CENC) s více technologiemi DRM. Hlavní trend v online streamování a OTT odvětví je pro použití šifrování CENC s více nativní DRM na různých platformách klienta. Tohoto trendu je shift od předchozí verze, který používá jeden DRM a jeho klientskou sadou SDK pro různé platformy klienta. Pokud používáte šifrování CENC s více nativní DRM, technologie PlayReady a Widevine jsou šifrované podle [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikace.

Výhody CENC s více technologiemi DRM jsou to:

* Snižuje náklady na šifrování, protože proces jeden šifrování se používá pro různé platformy s jeho nativní technologiemi DRM.
* Snižuje náklady na správu šifrované prostředky, protože je potřeba jenom jednu kopii šifrované prostředky.
* Eliminuje DRM klientů na licence náklady, protože je obvykle volné v jeho nativní platforma Nativní klient DRM.

Společnost Microsoft se active vykonavatel DASH a šifrování CENC společně s některé přehrávače hlavní odvětví. Azure Media Services poskytuje podporu pro DASH a šifrování CENC. Poslední oznámení naleznete v těchto blozích:

*  [Uvedení služeb doručování licence Google Widevine ve službě Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services přidá balení Google Widevine pro doručení datový proud s více technologiemi DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>Přehled tohoto článku
Cílem tohoto článku je:

* Zadejte odkaz na návrh podsystému DRM, který používá šifrování CENC s více technologiemi DRM.
* Zadejte odkaz na implementaci pro platformu Azure/Media Services.
* Popisují některé témata týkající se návrhu a implementace.

Výraz "více technologiemi DRM" v článku, zahrnuje následující produkty:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Následující tabulka shrnuje nativní platforma/nativní aplikaci a prohlížeče nepodporuje každý DRM.

| **Klientské platformy** | **Nativní podpora DRM** | **Aplikace nebo prohlížeče** | **Formátů streamování** |
| --- | --- | --- | --- |
| **Inteligentní televizní přijímače, operátor externích zařízení, externích OTT zařízení** |PlayReady především se stává, nebo Widevine, nebo jiné |Linux, Opera, WebKit, jiné |Různými formáty |
| **Zařízení s Windows 10 (počítače s Windows, tablety s Windows, Windows Phone, Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>Univerzální platforma pro Windows |DASH (pro HLS, technologie PlayReady není podporována.)<br/><br/>DASH, technologie Smooth Streaming (pro HLS, technologie PlayReady není podporována.) |
| **Zařízení se systémem Android (telefon, tablet, TV)** |Widevine |Chrome nebo EME |DASH, HLS |
| **iOS (iPhone, iPad), klienty OS X a Apple TV** |FairPlay |Safari 8 +/ EME |HLS |


Vzhledem k tomu, aktuální stav nasazení pro každý DRM služba obvykle chce implementovat dvě nebo tři technologiemi DRM a ujistěte se, že adresa všechny typy koncových bodů nejlepším způsobem.

Je kompromis mezi složitosti logice služby a složitost na straně klienta k dosažení určitá úroveň činnost koncového uživatele na různých klientech.

Chcete-li váš výběr, mějte na paměti:

* PlayReady je nativně implementovaná v každé zařízení s Windows, na některých zařízeních se systémem Android a dostupný prostřednictvím sady SDK softwaru na prakticky jakékoli platformě.
* Widevine je nativně implementována v každé zařízení se systémem Android, Chrome a některá další zařízení.
* FairPlay je k dispozici jenom v iOS a Mac OS klienty nebo prostřednictvím iTunes.

Existují dvě možnosti pro typické více technologiemi DRM:

* PlayReady a Widevine
* PlayReady a Widevine, FairPlay

## <a name="a-reference-design"></a>Návrh odkazu
Tato část představuje odkaz návrh, který nerozlišuje technologie použít k implementaci.

Subsystém DRM může obsahovat následující součásti:

* Správa klíčů
* Balení DRM
* Doručování licencí DRM
* Kontrola oprávnění
* Ověřování/autorizace
* Přehrávač
* Sítě pro doručování původ nebo obsahu (CDN)

Následující diagram znázorňuje vysoké úrovně interakce mezi jednotlivými součástmi v subsystému DRM:

![Subsystému DRM s CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Návrh má tři základní vrstvy:

* Vrstva back-office (černá) nebude vystavena, externě.
* Vrstva DMZ (tmavý modrá) obsahuje všechny koncové body, které čelí veřejnosti.
* Veřejné internetové vrstvy (světla modrá) obsahuje CDN a přehrávače s přenosy dat mezi veřejného Internetu.

Je také třeba nástroj pro správu obsahu na ovládací prvek ochrany DRM, bez ohledu na to, jestli je statické nebo dynamické šifrování. Vstupy pro šifrování DRM patří:

* MBR obsahu videa
* Klíč obsahu
* Získání licence adresy URL

Zde je podrobný postup při přehrávání čas:

* Ověření uživatele.
* Autorizační token se vytvoří pro uživatele.
* DRM chráněný obsah (manifestu) se stáhne do přehrávač.
* Přehrávač odešle požadavek na licenční pořízení licenčních serverů spolu s klíčem ID a autorizační token.

Následující část popisuje návrh správy klíčů.

| **ContentKey – prostředek** | **Scénář** |
| --- | --- |
| 1Na-1 |Nejjednodušším případě. Poskytuje nejlepší ovládacího prvku. Ale výsledkem tohoto uspořádání obecně nejvyšší doručení náklady na licence. Minimálně je vyžadována pro každý chráněný prostředek jednu žádost o licenci. |
| 1m |Můžete použít stejný klíč k obsahu pro více prostředků. Například pro všechny prostředky v logické skupiny, například genre nebo do něj podmnožinu genre (nebo gen film), můžete vytvořit jeden klíč obsahu. |
| N-1 |Více klíčů k obsahu, je potřeba pro každý prostředek. <br/><br/>Například pokud se mají použít dynamické ochranu CENC s více technologiemi DRM pro MPEG-DASH a dynamické šifrování AES-128 pro HLS, je třeba dva samostatné klíče obsahu. Každý klíč k obsahu musí vlastní ContentKeyType. (Pro klíč obsahu používat pro dynamické šifrování CENC ochranu použijte ContentKeyType.CommonEncryption. Pro klíče obsahu pro dynamické šifrování AES-128 použijte ContentKeyType.EnvelopeEncryption.)<br/><br/>Například v CENC ochrany obsahu DASH, teoreticky, můžete použít jeden klíč obsahu k ochraně datový proud videa a jiné klíč obsahu k ochraně zvuk datového proudu. |
| M m |Kombinace předchozí dva scénáře. Jednu sadu obsahu klíče se používá pro každou z více prostředků ve stejné skupině asset. |

Dalším důležitým faktorem vzít v úvahu je použití trvalé a zajišťováno licence.

Tyto aspekty jsou důležité

Pokud používáte veřejného cloudu pro doručování licencí, trvalé a zajišťováno licence mít přímý vliv na doručování náklady na licence. Pro ilustraci mají následujících dvou různých případech:

* Měsíční předplatné: použití trvalé licence a obsahu klíč asset mapování 1: n. Například pro všechny děti filmy, používáme jeden klíč obsahu pro šifrování. V takovém případě:

    Celkový počet licencí, které jsou požadované pro děti všechny filmy a zařízení = 1

* Měsíční předplatné: použijte zajišťováno licence a mapování 1: 1 mezi klíč obsahu a asset. V takovém případě:

    Celkový počet licencí, které jsou požadované pro děti všechny filmy a zařízení = [počet filmy sledovaná] x [počet relací, které]

Dvou různých návrzích vést k velmi jiné licenční vzory požadavku. Různé vzorce za následek doručení jiné licenční náklady Pokud službu doručování licencí poskytuje veřejného cloudu, jako je například služba Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Mapování návrhu technologie pro implementaci
V dalším kroku obecné návrhu je namapována na technologie na platformě Azure/Media Services zadáním technologii, kterou chcete použít pro každý stavební blok.

V následující tabulce jsou uvedeny mapování.

| **Stavební blok.** | **Technologie** |
| --- | --- |
| **Player** |[Přehrávač médií Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Zprostředkovatel identity (IDP)** |Azure Active Directory (Azure AD) |
| **Služby tokenů zabezpečení (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Dynamické ochrany Media Services |
| **Doručování licencí DRM** |* Media Services licence doručení (PlayReady, Widevine, FairPlay) <br/>* Axinom licenčního serveru <br/>* Vlastní PlayReady licenčního serveru |
| **Počátek** |Koncový bod streamování Media Services |
| **Správa klíčů** |Není skutečně potřeba pro odkaz na implementaci |
| **Správa obsahu** |Konzolovou aplikaci C# |

Jinými slovy IDP i služby tokenů zabezpečení se používají s Azure AD. [Rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Media Services a Media Player podporovat DASH a šifrování CENC s více technologiemi DRM.

Následující diagram znázorňuje celkové struktuře a toku s předchozí mapování technologie:

![Šifrování CENC na Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Nastavit dynamické šifrování CENC, používá nástroj pro správu obsahu vstupy následující:

* Otevřete obsahu
* Klíč obsahu z klíče generace nebo správy
* Získání licence adresy URL
* Seznam informací ze služby Azure AD

Tady je výstup nástroje správy obsahu:

* ContentKeyAuthorizationPolicy obsahuje specifikaci na tom, jak doručování licencí ověří token JSON Web Token (JWT) a specifikace licence DRM.
* AssetDeliveryPolicy obsahuje specifikace na streamování formátu, ochrany DRM a adresy URL získání licence.

Tady je postup běhovém:

* Po ověření uživatele je vygenerována token JWT.
* Jedna z deklarací obsažené v token JWT je deklaraci skupiny, která obsahuje objekt ID EntitledUserGroup skupiny. Tato deklarace identity se používá k se kontrola nárok.
* Přehrávač stáhne obsah chráněný CENC manifest klienta a identifikuje následující:
   * ID klíče.
   * Obsah je CENC chráněný.
   * Licence získání adresy URL.
* Přehrávač odešle požadavek získání licence založené na prohlížeči nebo DRM podporované. V licenci pořízení žádosti, klíč, že jsou také odeslány ID a token JWT. Službu doručování licencí ověří token JWT a deklarace obsažené před vydává licence, které jsou potřebné.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Implementace postupů
Implementace zahrnuje následující kroky:

1. Připravte testovací prostředky. Kódování nebo balíček testu videu se více přenosovými rychlostmi fragmentovaný soubor MP4 ve službě Media Services. Tento prostředek je *není* DRM chráněný. DRM ochrany je potřeba dynamické ochrany později.

2. Vytvořte klíč ID a klíč obsahu (volitelně z klíče počáteční hodnoty). V tomto případě není nutné systémem správy klíčů, protože pouze jeden klíč je vyžadováno pro několik prostředků testovací ID a klíč obsahu.

3. Rozhraní API služby Media Services použijte ke konfiguraci služeb doručování licencí DRM více pro prostředek testu. Pokud používáte vlastní licenčních serverů ve vaší společnosti nebo vaše společnost dodavatelé místo licenční služby ve službě Media Services, můžete tento krok přeskočit. Získání licence adresy URL můžete zadat v kroku při konfiguraci doručování licencí. Rozhraní API služby Media Services je potřeba zadat některé podrobné konfigurace, jako je například omezení zásad autorizace a šablony odpovědi licence pro různé služby licence DRM. V tuto chvíli portál Azure neposkytuje rozhraní potřebných pro tuto konfiguraci. Informace na úrovni rozhraní API a ukázkový kód, najdete v části [použití PlayReady nebo Widevine běžného dynamického šifrování](media-services-protect-with-playready-widevine.md).

4. Konfigurace zásad doručení assetu pro prostředek testovací použijte rozhraní API služby média. Informace na úrovni rozhraní API a ukázkový kód, najdete v části [použití PlayReady nebo Widevine běžného dynamického šifrování](media-services-protect-with-playready-widevine.md).

5. Vytvořit a nakonfigurovat klienta služby Azure AD v Azure.

6. Vytvořte několik uživatelských účtů a skupin v klientovi služby Azure AD. Vytvořte alespoň skupinu "Uživatel s názvem" a přidat uživatele do této skupiny. Uživatelé v této skupině předat kontrolu oprávnění v získání licence. Uživatelé nejsou v této skupině služeb při selhání se kontrola ověřování a nelze získat licenci. Členství v této skupině "Uživatel s názvem" je taková deklarace požadovaných skupin v JWT vydaného Azure AD. Při konfiguraci služeb doručování licencí DRM více zadáte v kroku tento požadavek deklarace identity.

7. Vytvoření aplikace ASP.NET MVC pro hostování vaší přehrávání videa. Tato aplikace ASP.NET je chránit pomocí ověřování uživatele pomocí služby klienta Azure AD. Správné deklarace identity jsou součástí přístupových tokenů získat po ověření uživatele. Doporučujeme, abyste OpenID Connect rozhraní API pro tento krok. Nainstalujte následující balíčky NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Instalovat balíček Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Vytvoření přehrávač pomocí [rozhraní API služby Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Použít [rozhraní API služby Azure Media Player ProtectionInfo](http://amp.azure.net/libs/amp/latest/docs/) k určení technologii, která DRM používat na různých platformách DRM.

9. V následující tabulce jsou uvedeny testovací matrice.

    | **DRM** | **Prohlížeč** | **Výsledek nárok uživatele** | **Výsledek unentitled uživatele** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge nebo Internet Explorer 11 v systému Windows 10 |Úspěšné |Chyba |
    | **Widevine** |Chrome ve Windows 10 |Úspěšné |Chyba |
    | **FairPlay** |Bude doplněno | | |

Informace o tom, jak nastavit Azure AD pro aplikaci ASP.NET MVC player najdete v tématu [integraci aplikace založené na Azure Media Services OWIN MVC se službou Azure Active Directory a omezit klíče doručování obsahu na základě deklarací JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Další informace najdete v tématu [ověření pomocí tokenu JWT v Azure Media Services a dynamickým šifrováním](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Informace o Azure AD:

* Můžete najít informace pro vývojáře v [Příručka pro vývojáře Azure Active Directory](../../active-directory/active-directory-developers-guide.md).
* Můžete najít informace pro správce v [Správa adresáře služby Azure AD klienta](../../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Některé problémy v implementaci
Použijte následující informace o odstraňování potíží pro pomoc s potížích s implementací.

* Adresa URL musí končit vystavitele "/". Cílová skupina musí být ID player aplikace klienta. Navíc přidat "/" na konci URL vystavitele.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    V [JWT Decoder](http://jwt.calebb.net/), uvidíte **oblast** a **iss**, jak je znázorněno v token JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Přidejte oprávnění k aplikaci ve službě Azure AD na **konfigurace** aplikace. Oprávnění se vyžadují pro každou aplikaci, místní i nasazené verzi.

    ![Oprávnění](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Při nastavování dynamické šifrování CENC ochrany, použijte správný vystavitele.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Následující nefunguje:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identifikátor GUID je ID klienta Azure AD. Identifikátor GUID naleznete v **koncové body** místní nabídky na portálu Azure.

* Členství ve skupině udělit deklarací oprávnění. Ujistěte se, že toto je v souboru manifestu aplikace Azure AD: 

    "groupMembershipClaims": "Vše" (výchozí hodnota je null)

* Nastavte správné TokenType při vytváření omezení požadavků.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Vzhledem k tomu, abyste mohli přidat podporu pro token JWT (Azure AD) kromě SWT (ACS), je výchozí TokenType TokenType.JWT. Pokud používáte SWT/ACS, musíte nastavit na TokenType.SWT token.

## <a name="additional-topics-for-implementation"></a>Další témata pro implementaci
Tato část popisuje některé další témata v návrhu a implementace.

### <a name="http-or-https"></a>Protokol HTTP nebo HTTPS?
Aplikace ASP.NET MVC player musí podporovat tyto věci:

* Ověření uživatele prostřednictvím služby Azure AD, která je v rámci protokolu HTTPS.
* JWT exchange mezi klientem a Azure AD, která je v rámci protokolu HTTPS.
* Získání licence DRM klientem, který musí být v rámci protokolu HTTPS, pokud zajišťuje doručování licencí Media Services. Sady produktů PlayReady není vyžádá HTTPS pro doručování licencí. Pokud váš server licence PlayReady mimo Media Services, můžete pomocí protokolu HTTP nebo HTTPS.

Přehrávač aplikace ASP.NET používá protokol HTTPS jako osvědčený postup, tak, aby Media Player na stránce v rámci protokolu HTTPS. HTTP je však upřednostňované pro streamování, takže je potřeba zvážit problém smíšený obsah.

* V prohlížeči neumožňuje smíšený obsah. Ale můžete ho povolit moduly plug-in Silverlight a OSMF modulu plug-in pro funkce smooth a čárka. Smíšený obsah je z důvodu riziko, že možnost Vložit škodlivý JavaScript, což může způsobit zákaznická data za ohrožené zabezpečení. Prohlížeče blokovat tato funkce ve výchozím nastavení. Jediný způsob, jak je obejít je na straně serveru (původ) tím, že se všechny domény (bez ohledu na HTTPS nebo HTTP). To je pravděpodobně není vhodné buď.
* Nepoužívejte smíšený obsah. Aplikace přehrávače i Media Player měli používat protokol HTTP nebo HTTPS. Při přehrávání smíšený obsah, technická silverlightSS uděláte zrušením upozornění smíšený obsah. Technická flashSS zpracovává smíšený obsah bez upozornění smíšený obsah.
* Pokud před srpen 2014 byl vytvořen koncový bod streamování, nebudou podporovat protokol HTTPS. V takovém případě vytvořit a použít nový koncový bod streamování pro protokol HTTPS.

V implementaci odkaz pro DRM chráněný obsah, aplikace a streamování jsou v rámci protokolu HTTPS. Otevřete obsah přehrávač nepotřebuje ověřování nebo licenci, abyste mohli používat protokol HTTP nebo HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory podepisování výměna klíče
Podepisování výměně klíčů je důležité vzít vzít v úvahu při implementaci. Pokud ho ignorovat dokončení systému nakonec přestane fungovat úplně, do šesti týdnů nejvíce.

Azure AD používá oborových standardů k navázání vztahu důvěryhodnosti mezi samostatně a aplikace, které používají Azure AD. Konkrétně Azure AD používá podpisový klíč, který se skládá z pár veřejného a privátního klíče. Pokud Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli, je podepsaný službou Azure AD s privátním klíčem, než budou odeslána zpět do aplikace. Pokud chcete ověřit, zda je token platný a původu z Azure AD, musíte aplikaci ověřit podpis tokenu. Aplikace používá veřejný klíč vystavený službou Azure AD, která je součástí dokument federačních metadat klienta. Tento veřejný klíč a podpisový klíč, ze kterého pochází, je stejný jako ten, používá se pro všechny klienty v Azure AD.

Další informace o výměně klíčů Azure AD najdete v tématu [důležité informace o podepisování výměna klíče ve službě Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mezi [pár veřejného a privátního klíče](https://login.microsoftonline.com/common/discovery/keys/):

* Privátní klíč se používá Azure AD vygenerovat token JWT.
* Veřejný klíč se používají aplikaci, například služeb doručování licencí DRM ve službě Media Services k ověření tokenů JWT.

Z bezpečnostních důvodů Azure AD otočí certifikát pravidelně (každých šest týdnů). V případě porušení zabezpečení může dojít výměny klíčů, kdykoli. Proto služeb doručování licence ve službě Media Services je nutné aktualizovat veřejný klíč používaný jako Azure AD otočí pár klíčů. Jinak tokenu ověřování ve službě Media Services selže a vydání žádná licence.

Chcete-li nastavit tuto službu, nastavte TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument při konfiguraci služeb doručování licencí DRM.

Tady je postup JWT:

* Azure AD vydá token JWT s aktuální privátní klíč pro ověřené uživatele.
* Když přehrávač uvidí šifrování CENC s více technologiemi DRM chráněný obsah, nejdřív vyhledat JWT vydaného Azure AD.
* Přehrávač odešle žádost o získání licence s token JWT služeb doručování licence ve službě Media Services.
* Služeb doručování licence ve službě Media Services použít aktuální nebo platný veřejný klíč z Azure AD ověřit token JWT předtím, než vystavování licencí.

Služeb doručování licencí DRM vždy zkontrolujte aktuální nebo platný veřejný klíč z Azure AD. Veřejný klíč předloží Azure AD je klíč používaný k ověření token JWT vydaného Azure AD.

Co v případě výměny klíčů se stane po Azure AD vygeneruje token JWT, ale předtím, než odešle token JWT hráči, služeb doručování licencí DRM ve službě Media Services k ověření?

Vzhledem k tomu, že klíč můžete převracet v každém okamžiku, více než jeden platný veřejný klíč je vždy k dispozici v dokument federačních metadat. Doručování licencí Media Services můžete použít některou z klíče určené v dokumentu. Protože jeden klíč může být vrácena brzy, jiné může být jeho nahrazení a tak dále.

### <a name="where-is-the-access-token"></a>Kde je tokenu přístupu?
Pokud se podíváte na tom, jak webová aplikace volá aplikaci API v části [identita aplikace s udělení pověření klienta OAuth 2.0](../../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), tok ověřování vypadá takto:

* Uživatel se přihlásí ke službě Azure AD ve webové aplikaci. Další informace najdete v tématu [webový prohlížeč k webové aplikaci](../../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* Koncový bod autorizace Azure AD přesměruje uživatelského agenta zpět do klientské aplikace s autorizační kód. Uživatelský agent vrátí autorizační kód identifikátor URI přesměrování klienta aplikace.
* Webová aplikace musí získat přístupový token, aby mohli ověřit webové rozhraní API a načíst požadovaný prostředek. Vytvoří žádost o token koncový bod služby Azure AD a poskytuje pověření, ID klienta a identifikátor ID URI webové rozhraní API aplikace. Představuje autorizační kód k prokázání, že dá souhlas uživatele.
* Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
* Přes protokol HTTPS webová aplikace používá přístupový token vrácená JWT pro přidání JWT řetězec s označením "Nosiče" "Autorizační" hlavičky požadavku do webové rozhraní API. Webové rozhraní API pak ověří token JWT. Pokud je ověření úspěšné, vrátí požadovaného prostředku.

V tomto toku identity aplikace webové rozhraní API důvěřuje, že webová aplikace ověřil daného uživatele. Z tohoto důvodu se tento vzor nazývá důvěryhodný subsystém. [Autorizace vývojový diagram](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) popisuje, jak-udělení autorizačního kódu-toku funguje.

Získání licence s omezení s tokenem používá se stejný vzor důvěryhodný subsystém. Službu doručování licencí ve službě Media Services je webové rozhraní API prostředků, nebo "prostředek back-end", který potřebuje přístup k webové aplikaci. Kde je proto tokenu přístupu?

Přístupový token se získávají z Azure AD. Po úspěšné ověření uživatele je vrácena autorizační kód. Autorizační kód se pak používá, společně s ID klienta a klíč aplikace do systému exchange pro přístupový token. Přístupový token se používá pro přístup k aplikaci "ukazatel", který odkazuje na nebo představuje službu doručování licencí Media Services.

Registrace a konfigurace ukazatel aplikace ve službě Azure AD, proveďte následující kroky:

1. V klientovi Azure AD:

   * Přidání aplikace (prostředků) s https://[resource_name].azurewebsites.net/ adresa URL přihlašování. 
   * Přidáte ID aplikace s URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Přidejte nový klíč pro aplikaci prostředků.

3. Aktualizace souboru manifest aplikace, tak, aby vlastnost groupMembershipClaims má hodnotu "groupMembershipClaims": "Vše".

4. V aplikaci Azure AD, která odkazuje na webovou aplikaci player, v části **oprávnění k ostatním aplikacím**, přidání aplikace prostředků, která byla přidána v kroku 1. V části **delegovaná oprávnění**, vyberte **přístupu [resource_name]**. Tato možnost dává oprávnění webové aplikace k vytvoření přístupové tokeny, které přístup k aplikaci prostředků. Pokud vyvíjíte pomocí sady Visual Studio a webové aplikace Azure, to provést pro místní a nasazené verze webové aplikace.

JWT vydaného Azure AD se používá pro přístup k prostředku ukazatel přístupový token.

### <a name="what-about-live-streaming"></a>Co se chystáte živé streamování?
Předchozí diskuse se zaměřuje na prostředky na vyžádání. Co se chystáte živé streamování?

Přesně stejnou návrh a implementaci můžete použít k ochraně živé streamování ve službě Media Services tak, že považuje asset přidružený program jako VOD asset.

Konkrétně jak živě Streamovat ve službě Media Services, musíte vytvořit kanál a potom vytvořit program v kanálu. Pokud chcete vytvořit program, potřebujete vytvořit asset, který obsahuje za provozu archivu programu. Zajistit CENC s více technologiemi DRM ochranu živý obsah stejný instalační program nebo zpracování na asset použijte jako by šlo VOD asset před spuštěním programu.

### <a name="what-about-license-servers-outside-media-services"></a>Co licenčních serverů mimo Media Services?
Zákazníci často, investovali do serverové farmy licence buď v datovém centru nebo jeden hostované poskytovatelé služeb DRM. Pomocí Media Services Content Protection můžou fungovat v hybridní režim. Obsah můžete hostované a dynamicky chráněný ve službě Media Services, když jsou servery mimo Media Services doručí licence DRM. V tomto případě zvažte následující změny:

* Služba tokenů zabezpečení je potřeba vystavovat tokeny, které jsou přijatelné a lze ověřit pomocí licence serverové farmy. Servery licence Widevine poskytované Axinom například vyžadovat konkrétní JWT, který obsahuje zprávu nárok. Proto je potřeba mít služby tokenů zabezpečení k vydávání takový token JWT. Pro informace o tomto typu implementace, přejděte na [centru dokumentace Azure](https://azure.microsoft.com/documentation/) a zobrazit [Axinom používá pro doručování licence na Widevine do služby Azure Media Services](media-services-axinom-integration.md).
* Už je nutné nakonfigurovat službu doručování licencí (ContentKeyAuthorizationPolicy) ve službě Media Services. Je třeba zadat adresy URL pro získání licence (pro PlayReady, Widevine a FairPlay) při konfiguraci AssetDeliveryPolicy nastavit šifrování CENC s více technologiemi DRM.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co dělat, když chcete používat vlastní službu tokenů zabezpečení?
Zákazník můžete použít vlastní službu tokenů zabezpečení zajistit tokeny Jwt. Z těchto důvodů:

* Rozšíření IDP využívaných zákazníkem nepodporuje službu tokenů zabezpečení. Vlastní službu tokenů zabezpečení v takovém případě může být možnost.
* Zákazník může být nutné flexibilní nebo náročnější ovládací prvek pro integraci služby tokenů zabezpečení zákazníka odběratele účetním systémem. Operátor MVPD může například nabízejí více balíčků OTT odběratele, jako je například premium a basic a sportu. Operátor chtít tak, aby odpovídaly deklarace do tokenu s balíčkem odběratele, takže jsou k dispozici pouze obsah v určitém balíčku. V takovém případě vlastní službu tokenů zabezpečení poskytuje potřebné flexibilitu a řízení.

Pokud používáte vlastní službu tokenů zabezpečení, musí být provedeny dvě změny:

* Když nakonfigurujete službu doručování licencí pro určitý prostředek, budete muset zadat klíč zabezpečení používané pro ověřování pomocí vlastních tokenů zabezpečení místo aktuální klíč ze služby Azure AD. (Další informace podle.) 
* Když se vygeneruje JTW token, je určen klíč zabezpečení místo privátní klíč pro aktuální X509 certifikátu ve službě Azure AD.

Existují dva typy bezpečnostních klíčů:

* Symetrický klíč: stejný klíč se používá ke generování a ověřit token JWT.
* Asymetrický klíč: pár veřejného a privátního klíče v X509 certifikát se používá s privátní klíč šifrování nebo vygenerovat token JWT a veřejný klíč k ověření tokenu.

> [!NOTE]
> Pokud používáte rozhraní .NET Framework nebo C# jako vývoj platformy, X509 certifikát používaný zabezpečení asymetrického klíče musí mít klíč o délce alespoň 2048. Toto je požadavek třídy System.IdentityModel.Tokens.X509AsymmetricSecurityKey v rozhraní .NET Framework. Jinak je došlo k následující výjimce:

> IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' pro podepisování nemůže být menší než '2048' bits.

## <a name="the-completed-system-and-test"></a>Dokončené systému a testování
Tato část vás provede následující scénáře v dokončené začátku do konce systému tak, aby základní přehled o chování může mít, než získat účet přihlášení:

* Pokud potřebujete scénáři zjistili:

    * U video prostředků hostované ve službě Media Services, které jsou buď bez ochrany nebo chráněných DRM, ale bez ověření pomocí tokenu (vydávání licencí kdo ho vyžádány), můžete otestovat ji bez přihlášení. Přepnout na protokolu HTTP, pokud vaše streamování videa přes protokol HTTP.

* Pokud potřebujete scénářem integrované začátku do konce:

    * Grafické prostředky v rámci dynamické DRM ochrany ve službě Media Services, se ověření pomocí tokenu a JWT generované Azure AD musíte se přihlásit.

Přehrávač webové aplikace a její přihlašování najdete v tématu [tento web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Přihlášení uživatele
K testování začátku do konce integrovaný systém DRM, musíte mít účet vytvořit nebo přidat.

Který účet?

I když služba Azure původně umožňovala přístup pouze uživatelé s účtem Microsoft, je nyní povolen přístup uživatelům z obou systémů. Všechny vlastnosti Azure nyní vztah důvěryhodnosti služby Azure AD pro ověřování a Azure AD ověřuje organizační uživatele. Vztah federace byl vytvořen, kde Azure AD důvěřuje systémem identit uživatelů účtů Microsoft spotřebitelské uživatele ověřovat. V důsledku toho Azure AD, můžete ověřovat hostovaný Microsoft účty Azure AD i nativní účty.

Protože Azure AD důvěřuje doména účtu Microsoft, můžete přidat všechny účty z jakéhokoli z následujících domén do vlastní Azure AD klienta a použít účet pro přihlášení:

| **Název domény** | **Domény** |
| --- | --- |
| **Doména klienta vlastní Azure AD** |somename.onmicrosoft.com |
| **Podnikové doméně** |microsoft.com |
| **Doména účtu Microsoft** |live.com, Outlook.com, hotmail.com |

Můžete kontaktovat kterýkoli z autoři tak, aby měl účet vytvořit nebo přidat za vás.

Na následujících snímcích obrazovky zobrazit různé přihlašovací stránky použít jinou doménu účty:

**Vlastní Azure AD klienta účet domény**: přizpůsobené přihlašovací stránky vlastní Azure AD klienta domény.

![Účet domény klienta vlastní Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft doménový účet s čipovou kartu**: přihlašovací stránky přizpůsobený podle Microsoft podnikové IT s dvoufaktorové ověřování.

![Účet domény klienta vlastní Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: na přihlašovací stránku účtu Microsoft k příjemce.

![Účet domény klienta vlastní Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití rozšíření šifrované média pro PlayReady
Moderní prohlížeč s šifrované rozšíření média (EME) pro podporu technologie PlayReady, jako je například Internet Explorer 11 počítači s Windows 8.1 nebo novějším a prohlížeč Microsoft Edge ve Windows 10 je PlayReady základní DRM pro EME.

![Použití EME PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Oblasti tmavý player totiž PlayReady ochrany brání v provádění snímek obrazovky chráněné videa.

Následující snímek obrazovky ukazuje player moduly plug-in a Microsoft Security Essentials (MSE) / EME podpora:

![Přehrávač moduly plug-in pro PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Umožňuje EME v Microsoft Edge a Internet Explorer 11 ve Windows 10 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) má být volána na zařízeních Windows 10, které ji podporují. PlayReady SL3000 odemkne tok obsahu rozšířené premium (4K, záhlaví) a nové obsahu modely doručení (pro rozšířený obsah).

Umožňuje zaměřit se na zařízení se systémem Windows, je PlayReady pouze DRM v části hardware, které jsou k dispozici na zařízení se systémem Windows (PlayReady SL3000). Streamování služby můžete použít PlayReady prostřednictvím EME nebo pomocí aplikace pro univerzální platformu Windows a nabízejí vyšší kvalitu videa s použitím technologie PlayReady SL3000 než jiné DRM. Obvykle obsahu až na 2 kB prochází Chrome nebo Firefox a obsahu až 4 kB prochází Microsoft Edge nebo Internet Explorer 11 nebo aplikace pro univerzální platformu Windows na stejné zařízení. Velikost závisí na nastavení služby a implementace.

#### <a name="use-eme-for-widevine"></a>Použití EME Widevine
Moderní prohlížeč s podporou EME nebo Widevine, jako je například Chrome 41 + na Windows 10, Windows 8.1, Mac OSX Yosemite a Chrome na Android bodu 4.4.4 je Google Widevine DRM za EME.

![Použití EME Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nezabrání provedení snímek obrazovky chráněné videa.

![Přehrávač moduly plug-in pro Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled uživatelé
Pokud uživatel není členem skupiny "Uživatelé s názvem", neprojde uživatele zkontrolujte oprávnění. Licenční služby více technologiemi DRM pak vydá požadovanou licenci, jak je znázorněno odmítá. Podrobný popis je "získat licenci se nezdařilo," což je tak, jak má.

![Unentitled uživatelé](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění služby tokenů zabezpečení vlastní
Pokud spustíte vlastní službu tokenů zabezpečení, token JWT je vydaný vlastní službu tokenů zabezpečení pomocí buď symetrický, nebo asymetrický klíč.

Následující snímek obrazovky ukazuje scénář, který používá symetrického klíče (pomocí chromu):

![Vlastní STS symetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který používá asymetrický klíč prostřednictvím X509 certifikátu (pomocí moderní prohlížeče Microsoft):

![Vlastní STS asymetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Ověření uživatele v obou předchozích případech zůstává stejný. Bude provedeno prostřednictvím služby Azure AD. Jediným rozdílem je, že tokeny Jwt vydal vlastní službu tokenů zabezpečení místo Azure AD. Při konfiguraci ochrany dynamické šifrování CENC omezení službu doručování licencí Určuje typ JWT, buď symetrický, nebo asymetrický klíč.

## <a name="summary"></a>Souhrn
Tento dokument popsané CENC s více nativní DRM a řízení přístupu prostřednictvím ověření pomocí tokenu, návrh a jeho implementace pomocí Azure Media Services a Media Player.

* Návrh odkazu se zobrazí obsahující všechny potřebné součásti v subsystému DRM nebo CENC.
* Odkaz na implementaci byl předložený na Azure Media Services a Media Player.
* Některá témata přímo účastní návrh a implementaci byly také popsané.

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
