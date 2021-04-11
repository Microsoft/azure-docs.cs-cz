---
title: Návrh systému ochrany obsahu s řízením přístupu pomocí Azure Media Services | Microsoft Docs
description: Přečtěte si informace o tom, jak licencovat sadu Microsoft Smooth Streaming pro porting pro klienta.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a4f151b597b57b77fa6517c6ea0d586c1106986
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017133"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Návrh systému ochrany obsahu s řízením přístupu pomocí Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

Vytváření a vytváření subsystému DRM (Správa digitálních práv) pro řešení OTT (Digital Rights Management) nebo online streamování online je složitý úkol. Operátoři a online poskytovatelé videí obvykle používají jako externí tuto úlohu pro specializované poskytovatele služeb DRM. Cílem tohoto dokumentu je poskytnout referenční návrh a implementaci komplexního subsystému DRM v řešení OTT nebo online streamování.

Cíloví čtenáři tohoto dokumentu jsou vývojáři, kteří pracují v subsystému DRM OTT nebo online řešení pro streamování a na více obrazovkách nebo čtenářích, kteří mají zájem o subsystémy DRM. Předpokladem je, že čtenáři jsou obeznámené s aspoň jednou z technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe Access.

V této diskuzi o DRM zahrnujeme i běžné šifrování (CENC) s více DRM. Hlavním trendem v online streamování a v OTTém průmyslu je použití CENC s více nativními technologiemi DRM na různých klientských platformách. Tento trend je posun od předchozí verze, která používala jednu DRM a její klientskou sadu SDK pro různé klientské platformy. Pokud používáte CENC s více nativními technologiemi DRM, jsou protokoly PlayReady i Widevine šifrované podle specifikace [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Výhodou CENC s více technologiemi DRM je to, že:

* Snižuje náklady na šifrování, protože jeden proces šifrování se používá k zacílení různých platforem na nativní několikanásobnou.
* Snižuje náklady na správu šifrovaných prostředků, protože je potřeba jenom jedna kopie šifrovaných prostředků.
* Eliminuje náklady na licencování klientů DRM, protože nativní klient DRM je obvykle zdarma na své nativní platformě.

Microsoft je aktivním povýším PŘERUŠOVANé a CENC společně s některými významnými průmyslovými hráči. Azure Media Services poskytuje podporu pro POMLČKu a CENC. Poslední oznámení najdete na následujících blogu:

*  [Uvedení služeb doručování licence Google Widevine ve službě Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services přidá balíček Google Widevine pro doručování datového proudu s více technologiemi DRM.](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Cíle článku

Cílem tohoto článku je:

* Poskytněte referenční návrh subsystému DRM, který používá CENC s více technologiemi DRM.
* Poskytněte referenční implementaci na platformě Azure/Media Services.
* Prodiskutujte témata týkající se návrhu a implementace.

V tomto článku se výraz "Multi-DRM" zabývá následujícími produkty:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Následující tabulka shrnuje nativní platformu/nativní aplikaci a prohlížeče podporované jednotlivými DRM.

| **Platforma klienta** | **Nativní podpora DRM** | **Prohlížeč/aplikace** | **Formáty streamování** |
| --- | --- | --- | --- |
| **Inteligentní televizory, operátor STBs, OTT STBs** |PlayReady primárně a/nebo Widevine a/nebo jiné |Linux, Opera, WebKit, jiné |Různé formáty |
| **Zařízení s Windows 10 (počítače s Windows, tablety s Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Univerzální platforma Windows |POMLČKa (pro HLS, PlayReady se nepodporuje)<br/><br/>POMLČKa, Smooth Streaming (pro HLS, PlayReady se nepodporuje) |
| **Zařízení s Androidem (telefon, tablet, TV)** |Widevine |Chrome/EME |POMLČKA, HLS |
| **iOS (iPhone, iPad), klienti OS X a Apple TV** |FairPlay |Safari 8 +/EME |HLS |

Vzhledem k tomu, že aktuální stav nasazení pro každou DRM, služba obvykle vyžaduje implementaci dvou nebo tří několikanásobnou, aby bylo zajištěno, že budete řešit všechny typy koncových bodů tak, aby byly nejlepším způsobem.

Existuje kompromis mezi složitostí logiky služby a složitostí na straně klienta, aby se dosáhlo určité úrovně uživatelského prostředí na různých klientech.

Udělejte si výběr:

* Technologie PlayReady je nativně implementovaná v každém zařízení s Windows, na některých zařízeních s Androidem a dostupná prostřednictvím softwarových sad SDK na prakticky libovolné platformě.
* Widevine se nativně implementuje na všech zařízeních s Androidem, v Chrome a v některých dalších zařízeních.
* FairPlay je k dispozici pouze pro klienty iOS a Mac OS nebo prostřednictvím iTunes.

K dispozici jsou dvě možnosti typické pro více DRM:

* PlayReady a Widevine
* PlayReady, Widevine a FairPlay

## <a name="a-reference-design"></a>Referenční návrh
V této části najdete referenční návrh, který se nezávislá na technologie používané k jeho implementaci.

Subsystém DRM může obsahovat následující součásti:

* Správa klíčů
* Balení DRM
* Doručování licencí DRM
* Kontrolu nároků
* Ověřování/autorizace
* Přehrávač
* Původ/Content Delivery Network (CDN)

Následující diagram znázorňuje interakci vysoké úrovně mezi součástmi v subsystému DRM:

![Subsystém DRM s CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Pracovní postup ochrany DRM** |Media Services dynamickou ochranu |
| **Doručování licencí DRM** |* Media Services doručování licencí (PlayReady, Widevine, FairPlay) <br/>* Licenční server Axinom <br/>* Vlastní licenční server PlayReady |
| **Zdroji** |Koncový bod streamování Media Services |
| **Správa klíčů** |Není nutné pro implementaci reference. |
| **Správa obsahu** |Konzolová aplikace v jazyce C# |

Jinými slovy se v Azure AD používají IDP i STS. [Rozhraní API pro Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Media Services i Media Player podporují POMLČKy a CENC s více technologiemi DRM.

Následující diagram ukazuje celkovou strukturu a tok s předchozím mapováním technologie:

![CENC na Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Pro nastavení dynamického šifrování CENC používá nástroj pro správu obsahu následující vstupy:

* Otevření obsahu
* Klíč obsahu z vytváření a správy klíčů
* Adresy URL pro získání licence
* Seznam informací z Azure AD

Tady je výstup nástroje pro správu obsahu:

* ContentKeyAuthorizationPolicy obsahuje specifikaci způsobu, jakým doručování licencí ověřuje JSON Web Token (JWT) a specifikace licencí DRM.
* AssetDeliveryPolicy obsahuje specifikace pro streamování, ochranu DRM a adresy URL pro získání licence.

Toto je tok za běhu:

* Po ověření uživatele se vygeneruje token JWT.
* Jedna z deklarací identity obsažených v tokenu JWT je deklarace skupiny, která obsahuje ID objektu skupiny EntitledUserGroup. Tato deklarace identity se používá k předání kontroly nároků.
* Přehrávač stáhne manifest klienta obsahu chráněný CENC a identifikuje následující:
   * ID klíče
   * Obsah je chráněn CENC.
   * Adresy URL pro získání licence.
* Přehrávač vytvoří žádost o získání licence na základě podporovaného prohlížeče/DRM. V žádosti o získání licence se také odesílají ID klíče a token JWT. Služba doručování licencí ověří token JWT a deklarace, které obsahovaly, před tím, než vystaví potřebné licence.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Postupy implementace
Implementace zahrnuje následující kroky:

1. Připravte prostředky testu. Zakódování nebo zabalení testovacího videa pro fragmentaci MP4 s více přenosovými rychlostmi v Media Services. Tento *prostředek není chráněn technologií DRM.* Ochrana DRM se provádí později dynamickou ochranou.

2. Vytvořte ID klíče a klíč obsahu (volitelně od počátečního klíče). V této instanci není potřeba systém správy klíčů, protože pro několik testovacích prostředků je potřeba jenom jedno ID klíče a klíč obsahu.

3. Pomocí rozhraní Media Services API můžete nakonfigurovat služby pro doručování licencí s více technologiemi DRM pro testovací prostředek. Pokud používáte vlastní licenční servery vaší společnosti nebo dodavatelů vaší společnosti namísto licenčních služeb v Media Services, můžete tento krok přeskočit. V kroku můžete zadat adresy URL pro získání licence, když nakonfigurujete doručování licencí. Rozhraní Media Services API je potřeba k určení některých podrobných konfigurací, jako jsou například omezení zásad autorizace a šablony odpovědí na licence pro různé licenční služby DRM. V tuto chvíli Azure Portal neposkytuje potřebné uživatelské rozhraní pro tuto konfiguraci. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md).

4. Použijte rozhraní Media Services API ke konfiguraci zásad doručení assetu pro testovací prostředek. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](media-services-protect-with-playready-widevine.md).

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

* Informace o vývojářích najdete v [příručce pro vývojáře Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md).
* Informace o Správci najdete ve [správě adresáře tenanta Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Některé problémy v implementaci
Pro pomoc s problémy s implementací použijte následující informace pro řešení potíží.

* Adresa URL vystavitele musí končit znakem "/". Cílovou skupinou musí být ID klienta aplikace aktéra. Na konec adresy URL vystavitele také přidejte znak "/".

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    V [dekodéru JWT](http://jwt.calebb.net/)vidíte **AUD** a **ISS**, jak je znázorněno v tokenu JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Přidejte do aplikace ve službě Azure AD oprávnění na kartě **Konfigurace** aplikace. Pro každou aplikaci jsou vyžadována oprávnění, místní i nasazená verze.

    ![Oprávnění](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Protože kromě služby SWT (ACS) přidáte podporu pro token JWT (Azure AD), výchozí typ TokenType je typ TokenType. JWT. Pokud používáte SWT/ACS, musíte nastavit token na typ TokenType. SWT.

## <a name="additional-topics-for-implementation"></a>Další témata pro implementaci
Tato část popisuje některá další témata týkající se návrhu a implementace.

### <a name="http-or-https"></a>HTTP nebo HTTPS?
Aplikace aktéra ASP.NET MVC musí podporovat následující:

* Ověřování uživatelů prostřednictvím služby Azure AD, která je v rámci protokolu HTTPS.
* Výměna JWT mezi klientem a službou Azure AD, která je v protokolu HTTPS.
* Získání licence DRM klientem, který musí být v rámci protokolu HTTPS, pokud je doručování licencí poskytované Media Services. Sada produktů PlayReady nevyžaduje pro doručování licencí protokol HTTPS. Pokud je váš licenční server PlayReady mimo Media Services, můžete použít buď protokol HTTP, nebo HTTPS.

Aplikace přehrávače ASP.NET používá jako osvědčený postup protokol HTTPS, takže Media Player se nachází na stránce s protokolem HTTPS. Protokol HTTP je ale upřednostňovaný pro streamování, takže je potřeba vzít v úvahu problém se smíšeným obsahem.

* Prohlížeč nepovoluje smíšený obsah. Moduly plug-in, jako je Silverlight a modul plug-in OSMF pro zajištění hladkého a PŘERUŠOVANého připojení, to umožňují. Smíšený obsah je bezpečnostní riziko z důvodu hrozby, která umožňuje vložit škodlivý JavaScript, což může způsobit ohrožení zákaznických dat. V prohlížečích je tato funkce ve výchozím nastavení zablokovaná. Jediným způsobem, jak se tento problém obejít, je na straně serveru (Origin) povolit všechny domény (bez ohledu na protokol HTTPS nebo HTTP). Tato možnost není pravděpodobně dobrý nápad.
* Vyhněte se Smíšenému obsahu. Aplikace přehrávače i Media Player by měly používat protokol HTTP nebo HTTPS. Při přehrávání smíšeného obsahu vyžaduje technologie Silverlight vymazávání upozornění na smíšený obsah. Technik Flash zpracovává smíšený obsah bez upozornění na smíšený obsah.
* Pokud byl koncový bod streamování vytvořen před srpna 2014, nebude podporovat protokol HTTPS. V takovém případě vytvořte a použijte nový koncový bod streamování pro protokol HTTPS.

V referenční implementaci obsahu chráněném pomocí DRM jsou aplikace i streamování v rámci protokolu HTTPS. V případě otevřeného obsahu přehrávač nepotřebuje ověření nebo licenci, takže můžete použít buď protokol HTTP, nebo HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Výměna Azure Active Directory podpisového klíče
Výměna podpisového klíče je důležitým bodem, který je potřeba vzít v úvahu při implementaci. Pokud ho ignorujete, dokončený systém nakonec přestane zcela fungovat, a to do šesti týdnů.

Azure AD používá oborové standardy k navázání vztahu důvěryhodnosti mezi sebou samými a aplikacemi, které používají Azure AD. Konkrétně služba Azure AD používá podpisový klíč, který se skládá z páru veřejného a privátního klíče. Když Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli, je před jeho odesláním zpět do aplikace podepsaný službou Azure AD s privátním klíčem. Pokud chcete ověřit, jestli je token platný a pochází z Azure AD, musí aplikace ověřit signaturu tokenu. Aplikace používá veřejný klíč vystavený službou Azure AD, který je obsažený v dokumentu federačních metadat klienta. Tento veřejný klíč a podpisový klíč, ze kterého je odvozený, je stejný, který se používá pro všechny klienty ve službě Azure AD.

Další informace o výměně klíčů Azure AD najdete v tématu [důležité informace o výměně klíčů v Azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md).

Mezi [dvojicí klíčů veřejného a soukromého](https://login.microsoftonline.com/common/discovery/keys/):

* Služba Azure AD používá k vygenerování tokenu JWT privátní klíč.
* Veřejný klíč používá aplikace, jako je třeba služba doručování licencí DRM v Media Services k ověření tokenu JWT.

Z bezpečnostních důvodů Azure AD pravidelně otáčí certifikát (každých šest týdnů). V případě porušení zabezpečení se může vyskytnout klávesová výměna kdykoli. Proto služba pro doručování licencí v Media Services musí aktualizovat veřejný klíč, který se používá jako Azure AD, který přetočí pár klíčů. V opačném případě se ověřování tokenu v Media Services nezdařilo a není vydána žádná licence.

Pokud chcete nastavit tuto službu, nastavte TokenRestrictionTemplate. OpenIdConnectDiscoveryDocument při konfiguraci služeb doručování licencí DRM.

Zde je tok JWT:

* Služba Azure AD vydá token JWT s aktuálním privátním klíčem pro ověřeného uživatele.
* Když hráč uvidí CENC s chráněným obsahem s více technologiemi DRM, nejprve vyhledá token JWT vydaný službou Azure AD.
* Přehrávač odešle požadavek na získání licence s tokenem JWT na služby pro doručování licencí v Media Services.
* Služby doručování licencí v Media Services používají aktuální/platný veřejný klíč z Azure AD k ověření tokenu JWT před vystavováním licencí.

Služba pro doručování licencí DRM vždy hledá aktuální a platný veřejný klíč z Azure AD. Veřejný klíč, který prezentuje služba Azure AD, je klíč, který slouží k ověření tokenu JWT vydaného službou Azure AD.

Co dělat, když služba Azure AD vygeneruje token JWT, ale před odesláním z přehrávačů do služby pro doručování licencí DRM v Media Services k ověření?

Vzhledem k tomu, že klíč může být kdykoli zavedený, v dokumentu federačních metadat je vždy k dispozici více než jeden platný veřejný klíč. Media Services doručování licencí může používat libovolný z klíčů uvedených v dokumentu. Vzhledem k tomu, že jeden klíč může být brzy vyměněn, další může být jeho nahrazení a tak dále.

### <a name="where-is-the-access-token"></a>Kde je přístupový token?
Pokud se podíváte, jak webová aplikace volá aplikaci API v části [Identita aplikace pomocí udělení přihlašovacích údajů klienta OAuth 2,0](../../active-directory/azuread-dev/web-api.md), bude tok ověřování následující:

* Uživatel se přihlásí do služby Azure AD ve webové aplikaci. Další informace najdete v tématu [webový prohlížeč pro webovou aplikaci](../../active-directory/azuread-dev/web-app.md).
* Koncový bod autorizace Azure AD přesměruje agenta uživatele zpět do klientské aplikace pomocí autorizačního kódu. Uživatelský agent vrátí autorizační kód k identifikátoru URI přesměrování klientské aplikace.
* Webová aplikace musí získat přístupový token, aby se mohl ověřit ve webovém rozhraní API a načíst požadovaný prostředek. Vytvoří požadavek na koncový bod tokenu Azure AD a poskytne přihlašovací údaje, ID klienta a identifikátor URI ID aplikace webového rozhraní API. Představuje autorizační kód, který potvrzuje, že uživatel souhlasí.
* Azure AD ověří aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
* Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "nosiče" do hlavičky "Authorization" žádosti webovému rozhraní API. Webové rozhraní API potom ověří token JWT. Pokud je ověření úspěšné, vrátí požadovaný prostředek.

V tomto toku identity aplikace webové rozhraní API důvěřuje, že webová aplikace ověřila daného uživatele. Z tohoto důvodu se tento model nazývá důvěryhodný podsystém. Vývojový [diagram autorizace](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) popisuje způsob, jakým funguje autorizační kód pro udělení kódu autorizace.

Získání licence s omezením tokenu se řídí stejným vzorem důvěryhodného subsystému. Služba doručování licencí v Media Services je prostředek webového rozhraní API, nebo "prostředek back-end", ke kterému musí mít webová aplikace přístup. Kde je přístupový token?

Přístupový token se získává z Azure AD. Po úspěšném ověření uživatele se vrátí autorizační kód. Pro výměnu přístupového tokenu se pak použije autorizační kód spolu s ID klienta a klíčem aplikace. Přístupový token se používá pro přístup k aplikaci "ukazatel", která odkazuje na nebo představuje službu Media Services License Delivery Service.

Pokud chcete zaregistrovat a nakonfigurovat aplikaci ukazatelů ve službě Azure AD, proveďte následující kroky:

1. V tenantovi Azure AD:

   * Přidejte aplikaci (prostředek) s přihlašovací adresou URL https://[resource_name]. azurewebsites. NET/. 
   * Přidejte ID aplikace s adresou URL https://[aad_tenant_name].. Microsoft. com/[resource_name].

2. Přidejte nový klíč pro aplikaci prostředků.

3. Aktualizujte soubor manifestu aplikace tak, aby vlastnost groupMembershipClaims měla hodnotu "groupMembershipClaims": "All".

4. V aplikaci Azure AD, která odkazuje na webovou aplikaci přehrávače, v části **oprávnění k ostatním aplikacím** přidejte aplikaci prostředků, kterou jste přidali v kroku 1. V části **delegovaná oprávnění** vyberte **přístup [resource_name]**. Tato možnost dává webové aplikaci oprávnění k vytváření přístupových tokenů, které přistupují k aplikaci prostředků. Tuto postup proveďte pro místní i nasazenou verzi webové aplikace, pokud vyvíjíte pomocí sady Visual Studio a webové aplikace Azure.

Token JWT vydaný službou Azure AD je přístupový token, který se používá pro přístup k prostředku ukazatele.

### <a name="what-about-live-streaming"></a>Co je živé streamování?
Předchozí diskuze zaměřená na prostředky na vyžádání. Co je živé streamování?

Můžete použít naprosto stejný návrh a implementaci k ochraně živého streamování v Media Services tím, že se Asset přidružený k programu považuje za prostředek VOD.

Konkrétně pro živé streamování v Media Services musíte vytvořit kanál a pak vytvořit program v rámci kanálu. Chcete-li vytvořit program, je nutné vytvořit Asset, který obsahuje živý archiv pro program. Aby bylo možné poskytovat CENC s ochranou obsahu s více technologiemi DRM, použijte stejné nastavení nebo zpracování na Asset, jako by se jednalo o VOD Asset před spuštěním programu.

### <a name="what-about-license-servers-outside-media-services"></a>Co jsou licenční servery mimo Media Services?
Zákazníci, kteří investovali do farmy licenčních serverů, jsou často ve svém vlastním datovém centru nebo v jednom hostovaném poskytovateli služeb DRM. Pomocí Media Services Content Protection můžete pracovat v hybridním režimu. Obsah je možné hostovat a dynamicky chránit v Media Services a při doručování licencí DRM servery mimo Media Services. V takovém případě zvažte následující změny:

* Služba tokenů zabezpečení musí vydávat tokeny, které jsou přijatelné a můžou je ověřit farmou licenčního serveru. Například licenční servery Widevine, které poskytuje Axinom, vyžadují konkrétní token JWT, který obsahuje zprávu nároku. Proto je potřeba, abyste měli k dispozici STS k vystavení takového tokenu JWT. Informace o tomto typu implementace najdete v [centru dokumentace Azure](https://azure.microsoft.com/documentation/) a v tématu [použití Axinom k doručování licencí Widevine do Azure Media Services](media-services-axinom-integration.md).
* V Media Services už nemusíte konfigurovat službu doručování licencí (ContentKeyAuthorizationPolicy). Pokud konfigurujete AssetDeliveryPolicy pro nastavení CENC s více DRM, musíte zadat adresy URL pro získání licence (pro PlayReady, Widevine a FairPlay).

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co když chci použít vlastní STS?
Zákazník se může rozhodnout použít vlastní STS k poskytnutí JWTs. Důvody zahrnují:

* IDP používaný zákazníkem nepodporuje službu STS. V takovém případě může být vlastní STS možnost.
* Zákazník může potřebovat flexibilní nebo užší řízení pro integraci služby STS se zákaznickým fakturačním systémem zákazníka. Například operátor MVPD může nabízet několik balíčků předplatitelů OTT, jako jsou Premium, Basic a sport. Operátor může chtít vyhledat deklarace identity v tokenu s balíčkem předplatitele, aby byl k dispozici pouze obsah v určitém balíčku. V takovém případě vlastní STS poskytuje potřebnou flexibilitu a kontrolu.

Pokud používáte vlastní STS, je třeba provést dvě změny:

* Při konfiguraci služby doručování licencí pro Asset musíte zadat klíč zabezpečení, který se používá pro ověření vlastní službou STS místo aktuálního klíče ze služby Azure AD. (Další podrobnosti následují.) 
* Když se vygeneruje token JTW, místo privátního klíče aktuálního certifikátu x509 ve službě Azure AD se zadá bezpečnostní klíč.

Existují dva typy klíčů zabezpečení:

* Symetrický klíč: stejný klíč se používá ke generování a ověření tokenu JWT.
* Asymetrická klávesa: v certifikátu x509 se používá privátní klíč privátního klíče pro šifrování/vygenerování JWT a s veřejným klíčem k ověření tokenu.

> [!NOTE]
> Použijete-li jako vývojovou platformu .NET Framework/C#, certifikát x509 použitý pro asymetrický bezpečnostní klíč musí mít délku klíče alespoň 2048. Toto je požadavek třídy System. IdentityModel. Tokens. X509AsymmetricSecurityKey v .NET Framework. V opačném případě je vyvolána následující výjimka:
> 
> IDX10630: System. IdentityModel. Tokens. X509AsymmetricSecurityKey pro Signing nemůže být menší než 2048 bitů.

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

![Snímek obrazovky zobrazující přizpůsobenou přihlašovací stránku vlastní domény tenanta Azure A D](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Účet domény Microsoft s čipovou kartou**: přihlašovací stránka přizpůsobená MICROSOFTem IT má dvojúrovňové ověřování.

![Snímek obrazovky, který zobrazuje přihlašovací stránku přizpůsobenou podnikem I T s ověřováním pomocí dvou faktorů.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: přihlašovací stránka účet Microsoft pro příjemce.

![Vlastní účet domény tenanta Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití šifrovaných rozšíření médií pro PlayReady
V moderním prohlížeči s rozšířeními EME (Encrypted Media Extensions) pro podporu PlayReady, jako je Internet Explorer 11 v Windows 8.1 nebo novějším a v prohlížeči Microsoft Edge ve Windows 10, je PlayReady základní DRM pro EME.

![Použití EME pro PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Tmavá oblast přehrávače je, protože ochrana PlayReady zabraňuje tomu, aby se snímek obrazovky s chráněným videem zabránil.

Následující snímek obrazovky ukazuje moduly plug-in přehrávače a podpora programu Microsoft Security Essentials (MSE)/EME:

![Moduly plug-in přehrávače pro PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME na Microsoft Edge a Internet Explorer 11 ve Windows 10 umožňuje, aby se [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) vyvolal na zařízeních s Windows 10, která ho podporují. PlayReady SL3000 odemkne tok vylepšeného obsahu Premium (4K, HDR) a nové modely doručování obsahu (pro lepší obsah).

V zařízeních s Windows se můžete soustředit jenom na hardware, který je k dispozici na zařízeních s Windows (PlayReady SL3000). Služba streamování může používat PlayReady prostřednictvím EME nebo prostřednictvím aplikace Univerzální platforma Windows a nabízí vyšší kvalitu videa pomocí technologie PlayReady SL3000 než jiné DRM. Obvykle se obsah až do 2K toků prostřednictvím Chrome nebo Firefox a obsah až do 4K toků přes Microsoft Edge/Internet Explorer 11 nebo aplikace Univerzální platforma Windows na stejném zařízení. Tato hodnota závisí na nastavení a implementaci služby.

#### <a name="use-eme-for-widevine"></a>Použití EME pro Widevine
V moderním prohlížeči s podporou EME/Widevine, jako je například Chrome 41 + ve Windows 10, Windows 8.1, Mac OSX Yosemite a Chrome v Androidu 4.4.4, je Google Widevine je technologie DRM za EME.

![Použití EME pro Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nebrání v vytváření snímku obrazovky chráněného videa.

![Moduly plug-in přehrávače pro Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Neoprávnění uživatelé
Pokud uživatel není členem skupiny "oprávnění uživatelé", uživatel neprojde kontrolu nároků. Licenční služba s více technologiemi DRM pak odmítne vystavit požadovanou licenci, jak je znázorněno na obrázku. Podrobný popis je "získání licence se nezdařilo", což je navrženo.

![Neoprávnění uživatelé](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění vlastní služby tokenu zabezpečení
Pokud spustíte vlastní STS, vystaví se token JWT vlastní službou STS pomocí symetrického nebo asymetrického klíče.

Následující snímek obrazovky ukazuje scénář, který používá symetrický klíč (pomocí Chromu):

![Vlastní STS s symetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který používá asymetrický klíč prostřednictvím certifikátu x509 (pomocí moderního prohlížeče Microsoftu):

![Vlastní STS s asymetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

V obou předchozích případech zůstává ověřování uživatelů stejné. Probíhá přes Azure AD. Jediným rozdílem je, že JWTs vydávají vlastní STS místo Azure AD. Když konfigurujete dynamickou ochranu CENC Protection, omezení služby doručování licencí určuje typ JWT, buď symetrický, nebo asymetrický klíč.

## <a name="summary"></a>Souhrn

Tento dokument popisuje CENC s více nativními technologiemi DRM a Access Control přes ověřování tokenů, jeho návrh a implementaci pomocí Azure, Media Services a Media Player.

* Byl předložen referenční návrh, který obsahuje všechny nezbytné komponenty v subsystému DRM/CENC.
* Referenční implementace byla předložena v Azure, Media Services a Media Player.
* Některá témata přímo zapojená do návrhu a implementace byly také popsána.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba od společnosti Google Inc. v souladu s podmínkami služby a zásadami ochrany osobních údajů Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
