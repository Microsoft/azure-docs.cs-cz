---
title: Návrh systému ochrany obsahu s řízením přístupu pomocí služby Azure Media Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak licencovat sadu Microsoft Smooth Streaming Client Porting Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162986"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Návrh systému ochrany obsahu s řízením přístupu pomocí Azure Media Services 

## <a name="overview"></a>Přehled

Navrhování a vytváření subsystému správy digitálních práv (DRM) pro řešení over-the-top (OTT) nebo online streaming je složitý úkol. Operátoři/poskytovatelé online videa obvykle zadávají tento úkol specializovaným poskytovatelům služeb DRM. Cílem tohoto dokumentu je představit referenční návrh a implementaci komplexního subsystému DRM v OTT nebo online streamovacím řešení.

Cílené čtečky pro tento dokument jsou inženýři, kteří pracují v subsystémech DRM OTT nebo online streaming / multiscreen řešení nebo čtenáři, kteří se zajímají o subsystémy DRM. Předpokládá se, že čtenáři jsou obeznámeni alespoň s jednou z technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe Access.

V této diskusi o DRM, jsme také společné šifrování (CENC) s multi-DRM. Hlavním trendem v on-line streamingu a OTT průmyslu je použití CENC s vícenativnídružidnou DRM na různých klientských platformách. Tento trend je posun od předchozího, který používal jeden DRM a jeho klientsdka pro různé klientské platformy. Při použití CENC s multi-nativní DRM, playready a Widevine jsou šifrovány podle [specifikace společné šifrování (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Výhody CENC s multi-DRM spo.

* Snižuje náklady na šifrování, protože jeden proces šifrování se používá k cílení na různé platformy s jeho nativní DRM.
* Snižuje náklady na správu šifrovaných datových zdrojů, protože je potřeba pouze jedna kopie šifrovaných datových zdrojů.
* Eliminuje náklady na licencování klientů DRM, protože nativní klient DRM je obvykle zdarma na své nativní platformě.

Microsoft je aktivním propagátorem DASH a CENC spolu s některými významnými průmyslovými hráči. Azure Media Services poskytuje podporu pro DASH a CENC. Poslední oznámení najdete v následujících blozích:

*  [Uvedení služeb doručování licence Google Widevine ve službě Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Mediální služby Azure přidávají obaly Google Widevine pro doručování datového proudu s více DRM](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Cíle článku

Cílem tohoto článku je:

* Poskytněte referenční návrh subsystému DRM, který používá CENC s multi-DRM.
* Poskytněte referenční implementaci na platformě Azure/Media Services.
* Diskutujte o některých tématech návrhu a implementace.

V článku termín "multi-DRM" zahrnuje následující produkty:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Následující tabulka shrnuje nativní platformu nebo nativní aplikaci a prohlížeče podporované jednotlivými driody DRM.

| **Platforma klienta** | **Nativní podpora DRM** | **Prohlížeč/aplikace** | **Formáty datových proudů** |
| --- | --- | --- | --- |
| **Chytré televizory, operátorstva, OTT StBs** |PlayReady především, a / nebo Widevine, a / nebo jiné |Linux, Opera, WebKit, ostatní |Různé formáty |
| **Zařízení s Windows 10 (PC s Windows, tablety s Windows, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Univerzální platforma Windows |DASH (pro HLS není playready podporováno)<br/><br/>DASH, Plynulé streamování (pro HLS není služba PlayReady podporována) |
| **Zařízení se systémem Android (telefon, tablet, TV)** |Widevine |Chrom/EME |DASH, HLS |
| **iOS (iPhone, iPad), klienti OS X a Apple TV** |FairPlay |Safari 8+/EME |HLS |

Vzhledem k aktuálnímu stavu nasazení pro každý DRM, služba obvykle chce implementovat dva nebo tři DRM a ujistěte se, že adresu všech typů koncových bodů v nejlepším způsobem.

Existuje kompromis mezi složitostí logiky služby a složitostí na straně klienta k dosažení určité úrovně uživatelského prostředí u různých klientů.

Chcete-li provést výběr, mějte na paměti:

* PlayReady je nativně implementován v každém zařízení se systémem Windows, na některých zařízeních Android a je k dispozici prostřednictvím softwarových sad SDK prakticky na libovolné platformě.
* Widevine je nativně implementována v každém zařízení Android, v Chromu a v některých dalších zařízeních.
* FairPlay je k dispozici pouze pro klienty iOS a Mac OS nebo prostřednictvím iTunes.

Existují dvě možnosti pro typické multi-DRM:

* PlayReady a Widevine
* PlayReady, Widevine a FairPlay

## <a name="a-reference-design"></a>Referenční návrh
Tato část představuje referenční návrh, který je agnostik k technologiím používaným k jeho implementaci.

Subsystém DRM může obsahovat následující součásti:

* Správa klíčů
* Balení DRM
* Doručování licencí DRM
* Kontrola nároků
* Ověřování/autorizace
* Přehrávač
* Síť pro doručování původu/obsahu (CDN)

Následující diagram znázorňuje interakci na vysoké úrovni mezi součástmi v subsystému DRM:

![Drm subsystém s CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Konstrukce má tři základní vrstvy:

* Vrstva back-office (černá) není externě vystavena.
* Vrstva DMZ (tmavě modrá) obsahuje všechny koncové body, které čelí veřejnosti.
* Veřejná internetová vrstva (světle modrá) obsahuje CDN a přehrávače s provozem přes veřejný internet.

K dispozici by měl být také nástroj pro správu obsahu pro řízení ochrany DRM, bez ohledu na to, zda se jedná o statické nebo dynamické šifrování. Vstupy pro šifrování DRM zahrnují:

* Video obsah mbr
* Klíč obsahu
* Adresy URL získání licencí

Zde je tok na vysoké úrovni během doby přehrávání:

* Uživatel je ověřen.
* Pro uživatele je vytvořen autorizační token.
* Obsah chráněný DRM (manifest) je stažen do přehrávače.
* Přehrávač odešle žádost o získání licence na licenční servery spolu s ID klíče a autorizačním tokenem.

Následující část popisuje návrh správy klíčů.

| **ContentKey-to-asset** | **Scénář** |
| --- | --- |
| 1:1 |Nejjednodušší případ. Poskytuje nejlepší kontrolu. Ale toto uspořádání obecně vede k nejvyšším nákladům na doručení licence. Pro každý chráněný majetek je vyžadována minimálně jedna žádost o licenci. |
| 1:N |Stejný klíč obsahu můžete použít pro více datových zdrojů. Například pro všechny datové zdroje v logické skupině, například žánr nebo podmnožinu žánru (nebo filmového genu), můžete použít jednu klávesu obsahu. |
| Počet ku 1 |Pro každý datový zdroj je potřeba více klíčů obsahu. <br/><br/>Pokud například potřebujete použít dynamickou ochranu CENC s multi-DRM pro MPEG-DASH a dynamickým šifrováním AES-128 pro HLS, potřebujete dva samostatné klíče obsahu. Každý klíč obsahu potřebuje svůj vlastní ContentKeyType. (Pro klíč obsahu používaný pro dynamickou ochranu CENC použijte ContentKeyType.CommonEncryption. Pro klíč obsahu používaný pro dynamické šifrování AES-128 použijte ContentKeyType.EnvelopeEncryption.)<br/><br/>Jako další příklad, v CENC ochranu obsahu DASH, teoreticky, můžete použít jeden klíč obsahu k ochraně video stream a jiný klíč obsahu k ochraně audio proudu. |
| Relace několika k několika jiným |Kombinace předchozích dvou scénářů. Jedna sada klíčů obsahu se používá pro každý z více datových zdrojů ve stejné skupině datových zdrojů. |

Dalším důležitým faktorem, který je třeba zvážit, je použití trvalých a netrvalých licencí.

Proč jsou tyto úvahy důležité?

Pokud používáte veřejný cloud pro doručování licencí, trvalé a trvalé licence mají přímý dopad na náklady na doručení licencí. Následující dva různé případy návrhu slouží k ilustraci:

* Měsíční předplatné: Použijte trvalou licenci a mapování obsahu 1:N. Například pro všechny dětské filmy používáme pro šifrování jeden klíč obsahu. V tomto případě:

    Celkový počet požadovaných licencí pro všechny dětské filmy/zařízení = 1

* Měsíční předplatné: Použijte netrvalou licenci a mapování 1:1 mezi klíčem obsahu a datovým zdrojem. V tomto případě:

    Celkový počet požadovaných licencí pro všechny dětské filmy/zařízení = [počet sledovaných filmů] x [počet relací]

Výsledkem dvou různých návrhů jsou velmi odlišné vzory žádostí o licenci. Různé vzory mají za následek různé náklady na doručení licencí, pokud je služba doručování licencí poskytována veřejným cloudem, jako je například Služba Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Návrh mapy na technologii pro implementaci
V dalším případě obecný návrh je mapován na technologie na platformě Azure/Media Services určením, kterou technologii použít pro každý stavební blok.

V následující tabulce je zobrazeno mapování.

| **Stavební blok** | **Technologie** |
| --- | --- |
| **Přehrávač** |[Přehrávač médií Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Zprostředkovatel identity (IDP)** |Azure Active Directory (Azure AD) |
| **Služba bezpečnostních tokenů (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Dynamická ochrana mediálních služeb |
| **Doručování licencí DRM** |* Poskytování licencí media services (PlayReady, Widevine, FairPlay) <br/>* Licenční server Axinom <br/>* Vlastní PlayReady licenční server |
| **Zdroj** |Koncový bod streamování služby Media Services |
| **Správa klíčů** |Není potřeba pro referenční implementaci |
| **Správa obsahu** |Konzolová aplikace Jazyka C# |

Jinými slovy idp a STS se používají s Azure AD. Rozhraní [API přehrávače médií Azure](https://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Mediální služby i přehrávač médií podporují DASH a CENC s multi-DRM.

Následující diagram znázorňuje celkovou strukturu a tok s předchozím mapováním technologií:

![CENC o mediálních službách](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Chcete-li nastavit dynamické šifrování CENC, používá nástroj pro správu obsahu následující vstupy:

* Otevřený obsah
* Klíč obsahu z generování/správy klíčů
* Adresy URL získání licencí
* Seznam informací ze služby Azure AD

Zde je výstup nástroje pro správu obsahu:

* ContentKeyAuthorizationPolicy obsahuje specifikaci o tom, jak doručování licencí ověří specifikace json webový token (JWT) a DRM licence.
* AssetDeliveryPolicy obsahuje specifikace pro formát streamování, ochranu DRM a adresy URL získávání licencí.

Zde je tok za běhu:

* Při ověření uživatele je generován jwt.
* Jeden z deklarací obsažených v JWT je deklarace skupiny, která obsahuje ID objektu skupiny EntitledUserGroup. Toto tvrzení se používá k propustku kontrolou nároků.
* Přehrávač stáhne klientský manifest obsahu chráněného cenc a identifikuje následující:
   * ID klíče.
   * Obsah je chráněn cenc.
   * Adresy URL získání licencí.
* Přehrávač provede požadavek na získání licence na základě podporovaného prohlížeče/DRM. V žádosti o získání licence jsou také odeslány ID klíče a JWT. Služba doručování licencí ověří JWT a nároky obsažené před vydáním potřebné licence.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Prováděcí postupy
Implementace zahrnuje následující kroky:

1. Připravte testovací prostředky. Zakódovat/zabalit testovací video do fragmentovaného protokolu MP4 s více přenosovou rychlostí ve službě Media Services. Tento prostředek *není* chráněn driody. Ochrana DRM se provádí pomocí dynamické ochrany později.

2. Vytvořte ID klíče a klíč obsahu (volitelně z osiva klíče). V tomto případě není potřeba systém správy klíčů, protože pro několik testovacích prostředků je vyžadováno pouze jedno ID klíče klíče klíče a klíč obsahu.

3. Pomocí rozhraní API mediálních služeb nakonfigurujte služby doručování licencí více DRM pro testovací datový zdroj. Pokud používáte vlastní licenční servery vaší společností nebo dodavateli vaší společnosti namísto licenčních služeb v aplikaci Media Services, můžete tento krok přeskočit. Při konfiguraci doručování licencí můžete v kroku zadat adresy URL pro získání licencí. Rozhraní API mediálních služeb je potřeba k určení některých podrobných konfigurací, jako je například omezení zásad autorizace a šablony odpovědí na licence pro různé licenční služby DRM. V tuto chvíli portál Azure neposkytuje potřebné ui pro tuto konfiguraci. Informace na úrovni rozhraní API a ukázkový kód naleznete [v tématu Použití běžného dynamického šifrování PlayReady a/nebo Widevine](media-services-protect-with-playready-widevine.md).

4. Pomocí rozhraní API mediálních služeb nakonfigurujte zásady doručování datových zdrojů pro testovací datový zdroj. Informace na úrovni rozhraní API a ukázkový kód naleznete [v tématu Použití běžného dynamického šifrování PlayReady a/nebo Widevine](media-services-protect-with-playready-widevine.md).

5. Vytvořte a nakonfigurujte klienta Azure AD v Azure.

6. Vytvořte několik uživatelských účtů a skupin v tenantovi Azure AD. Vytvořte alespoň skupinu "Oprávněný uživatel" a přidejte uživatele do této skupiny. Uživatelé v této skupině projdou kontrolou nároků při získávání licencí. Uživatelé, kteří nejsou v této skupině, neprojdou kontrolou ověřování a nemohou získat licenci. Členství v této skupině "Oprávněný uživatel" je požadované skupiny deklarace v JWT vydané Azure AD. Tento požadavek deklarace aplikace zadáte v kroku při konfiguraci služby doručování licencí více DRM.

7. Vytvořte aplikaci ASP.NET MVC pro hostování přehrávače videa. Tato ASP.NET aplikace je chráněna ověřováním uživatelů proti tenantovi Azure AD. Správné deklarace identity jsou zahrnuty v přístupových tokenech získaných po ověření uživatele. Pro tento krok doporučujeme rozhraní OpenID Connect API. Nainstalujte následující balíčky NuGet:

   * Instalační balíček Microsoft.Azure.ActiveDirectory.GraphClient
   * Instalační balíček Microsoft.Owin.Security.OpenIdConnect
   * Instalační balíček Microsoft.Owin.Security.Cookies
   * Instalační balíček Microsoft.Owin.Host.SystemWeb
   * Instalační balíček Microsoft.IdentityModel.Clients.ActiveDirectory

8. Vytvořte přehrávač pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, kterou technologii DRM použít na různých platformách DRM.

9. V následující tabulce je uvedena matice testu.

    | **Drm** | **Prohlížeč** | **Výsledek pro oprávněného uživatele** | **Výsledek pro uživatele bez oprávnění** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge nebo Internet Explorer 11 ve Windows 10 |Uspět |Neúspěch |
    | **Widevine** |Chrome, Firefox, Opera |Uspět |Neúspěch |
    | **Fairplay** |Safari na macOS      |Uspět |Neúspěch |
    | **AES-128** |Většina moderních prohlížečů  |Uspět |Neúspěch |

Informace o tom, jak nastavit Azure AD pro ASP.NET mvc přehrávač aplikace, najdete [v tématu Integrace aplikace Azure Media Services OWIN MVC s Azure Active Directory a omezit doručování klíčů obsahu na základě deklarací JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Další informace najdete v tématu [Ověřování tokenů JWT ve službě Azure Media Services a dynamické šifrování](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Informace o Azure AD:

* Informace o vývojáři najdete v [průvodci vývojářem služby Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md).
* Informace o správci najdete v [adresáři klienta Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Některé otázky při provádění
Nápovědu k problémům s implementací použijte následující informace o řešení potíží.

* Adresa URL vystavittele musí končit "/". Cílová skupina musí být ID klienta aplikace přehrávače. Také přidejte "/" na konci adresy URL vystavittele.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    V [Dekodéru JWT](http://jwt.calebb.net/)vidíte **aud** a **iss**, jak je znázorněno na JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Přidejte oprávnění k aplikaci ve službě Azure AD na kartě **Konfigurovat** aplikace. Oprávnění jsou vyžadována pro každou aplikaci, místní i nasazené verze.

    ![Oprávnění](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Při nastavované dynamické ochraně CENC použijte správný vystavitel.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Následující nefunguje:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identifikátor GUID je ID klienta Azure AD. Identifikátor GUID najdete v rozbalovací nabídce **Koncové body** na webu Azure Portal.

* Udělení oprávnění k členství ve skupině. Ujistěte se, že v souboru manifestu aplikace Azure AD je následující: 

    "groupMembershipClaims": "All" (výchozí hodnota je null)

* Nastavte správný TokenType při vytváření požadavků na omezení.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Vzhledem k tomu, že přidáte podporu pro JWT (Azure AD) kromě SWT (ACS), výchozí TokenType je TokenType.JWT. Pokud používáte SWT/ACS, musíte nastavit token token token.SWT.

## <a name="additional-topics-for-implementation"></a>Další témata pro implementaci
Tato část popisuje některá další témata v návrhu a implementaci.

### <a name="http-or-https"></a>HTTP nebo HTTPS?
Aplikace ASP.NET MVC player musí podporovat následující:

* Ověřování uživatelů prostřednictvím Azure AD, který je v části HTTPS.
* JWT exchange mezi klientem a Azure AD, který je v rámci protokolu HTTPS.
* Získání licence DRM klientem, které musí být v rámci protokolu HTTPS, pokud je doručení licence poskytováno službou Media Services. Sada produktů PlayReady nenařizuje protokol HTTPS pro doručování licencí. Pokud je licenční server PlayReady mimo službu Media Services, můžete použít protokol HTTP nebo HTTPS.

Aplikace ASP.NET přehrávačpoužívá jako osvědčený postup protokol HTTPS, takže media player je na stránce v části HTTPS. Protokol HTTP je však upřednostňován pro streamování, takže je třeba zvážit problém smíšeného obsahu.

* Prohlížeč nepovoluje smíšený obsah. Ale plug-iny jako Silverlight a OSMF plug-in pro hladké a DASH to umožňují. Smíšený obsah je bezpečnostní problém z důvodu hrozby schopnosti aplikovat škodlivý JavaScript, což může způsobit ohrožení dat zákazníků. Prohlížeče tuto funkci ve výchozím nastavení blokují. Jediný způsob, jak to obejít, je na straně serveru (původu) povolením všech domén (bez ohledu na protokol HTTPS nebo HTTP). To asi není dobrý nápad jeden.
* Vyhněte se smíšenému obsahu. Aplikace přehrávače i přehrávač médií by měly používat protokol HTTP nebo HTTPS. Při přehrávání smíšeného obsahu vyžaduje technologie silverlightSS vymazání upozornění na smíšený obsah. Technologie flashSS zpracovává smíšený obsah bez upozornění na smíšený obsah.
* Pokud byl koncový bod streamování vytvořen před srpnem 2014, nebude podporovat protokol HTTPS. V takovém případě vytvořte a použijte nový koncový bod streamování pro protokol HTTPS.

V referenční implementaci obsahu chráněného s driodami jsou aplikace i streamování pod protokolem HTTPS. Pro otevřený obsah přehrávač nepotřebuje ověřování ani licenci, takže můžete použít protokol HTTP nebo HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Přechod na podepisovací klíč služby Azure Active Directory
Podepisování klíče rollover je důležitý bod, který je třeba vzít v úvahu při implementaci. Pokud ji ignorujete, hotový systém nakonec přestane fungovat úplně, maximálně do šesti týdnů.

Azure AD používá oborové standardy k vytvoření vztahu důvěryhodnosti mezi sebou a aplikacemi, které používají Azure AD. Konkrétně Azure AD používá podpisový klíč, který se skládá z dvojice veřejného a soukromého klíče. Když Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli, je podepsán aslužba Azure AD s privátním klíčem před odesláním zpět do aplikace. Chcete-li ověřit, že token je platný a pochází z Azure AD, aplikace musí ověřit podpis tokenu. Aplikace používá veřejný klíč vystavený službou Azure AD, který je obsažen v dokumentu metadat federace klienta. Tento veřejný klíč a podpisový klíč, ze kterého je odvozen, je stejný klíč používaný pro všechny klienty ve službě Azure AD.

Další informace o přechodu klíče Azure AD najdete v [tématu Důležité informace o podepisování klíče rollover ve službě Azure AD](../../active-directory/active-directory-signing-key-rollover.md).

Mezi [dvojicí klíčů veřejného a soukromého sektoru](https://login.microsoftonline.com/common/discovery/keys/):

* Soukromý klíč se používá Azure AD ke generování JWT.
* Veřejný klíč je používán aplikací, jako je například služby doručování licencí DRM v media services k ověření JWT.

Z bezpečnostních důvodů Azure AD pravidelně otáčí certifikát (každých šest týdnů). V případě narušení zabezpečení může dojít k kdykoli. Služby doručování licencí ve službě Media Services proto musí aktualizovat veřejný klíč používaný jako Azure AD otočí dvojici klíčů. V opačném případě se ověření tokenu ve službě Media Services nezdaří a není vydána žádná licence.

Chcete-li tuto službu nastavit, nastavte tokenrestrictiontemplate.OpenIdConnectDiscoveryDocument při konfiguraci služeb doručování licencí DRM.

Zde je tok JWT:

* Azure AD problémy JWT s aktuální majek soukromý klíč pro ověřeného uživatele.
* Když hráč vidí CENC s více DRM chráněného obsahu, nejprve vyhledá JWT vydané Azure AD.
* Přehrávač odešle žádost o získání licence s JWT na doručovací služby v Media Services.
* Služby doručování licencí v Media Services použít aktuální/platný veřejný klíč z Azure AD k ověření JWT před vydáním licencí.

Služby doručování licencí DRM vždy zkontrolujte aktuální/platný veřejný klíč z Azure AD. Veřejný klíč prezentovaný službou Azure AD je klíč používaný k ověření JWT vydaného službou Azure AD.

Co když k přechodu na klíč dojde poté, co Azure AD vygeneruje JWT, ale před odesláním JWT hráči do služby doručování licencí DRM v Media Services pro ověření?

Vzhledem k tomu, že klíč lze kdykoli převrátit, je v dokumentu metadat federace vždy k dispozici více než jeden platný veřejný klíč. Doručování licencí služby Media Services může používat libovolný klíč uvedený v dokumentu. Vzhledem k tomu, že jeden klíč může být brzy vrácen, jiný může být jeho nahrazení, a tak dále.

### <a name="where-is-the-access-token"></a>Kde je přístupový token?
Pokud se podíváte na to, jak webová aplikace volá aplikaci rozhraní API pod [identitou aplikace s udělením pověření klienta OAuth 2.0](../../active-directory/azuread-dev/web-api.md), tok ověřování je následující:

* Uživatel se přihlásí k Azure AD ve webové aplikaci. Další informace naleznete [v tématu Webový prohlížeč webovým aplikacím](../../active-directory/azuread-dev/web-app.md).
* Koncový bod autorizace Azure AD přesměruje uživatelského agenta zpět do klientské aplikace s autorizačním kódem. Uživatelský agent vrátí autorizační kód identifikátoru URI přesměrování klientské aplikace.
* Webová aplikace potřebuje získat přístupový token, aby mohla ověřit webové rozhraní API a načíst požadovaný prostředek. Provede požadavek na koncový bod tokenu Azure AD a poskytuje pověření, ID klienta a identifikátor uri aplikace webového rozhraní API. Představuje autorizační kód, který dokazuje, že uživatel souhlasil.
* Azure AD ověřuje aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
* Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce "Autorizace" požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT. Pokud je ověření úspěšné, vrátí požadovaný prostředek.

V tomto toku identity aplikace webové rozhraní API důvěřuje, že webová aplikace ověřila uživatele. Z tohoto důvodu se tento vzor nazývá důvěryhodný subsystém. [Diagram toku autorizace](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) popisuje, jak funguje tok udělení autorizačního kódu.

Získání licence s omezením tokenu se řídí stejným vzorem důvěryhodného subsystému. Služba doručování licencí ve službě Media Services je prostředek webového rozhraní API nebo "back-end ový prostředek", ke kterému potřebuje přístup webová aplikace. Tak kde je přístupový token?

Přístupový token se získá ze služby Azure AD. Po úspěšném ověření uživatele je vrácen autorizační kód. Autorizační kód se pak používá spolu s ID klienta a klíčem aplikace k výměně za přístupový token. Přístupový token se používá pro přístup k aplikaci "ukazatel", který odkazuje na nebo představuje službu doručování licencí Media Services.

Pokud chcete zaregistrovat a nakonfigurovat aplikaci ukazatele ve službě Azure AD, postupujte takto:

1. V tenantovi Azure AD:

   * Přidejte aplikaci (prostředek) s přihlašovací adresou URL https://[resource_name].azurewebsites.net/. 
   * Přidejte ID aplikace s adresou URL https://[aad_tenant_name].onmicrosoft.com/[resource_name].

2. Přidejte nový klíč pro aplikaci prostředků.

3. Aktualizujte soubor manifestu aplikace tak, aby vlastnost groupMembershipClaims má hodnotu "groupMembershipClaims": "All".

4. V aplikaci Azure AD, která odkazuje na webovou aplikaci přehrávače, v části **Oprávnění k jiným aplikacím**přidejte aplikaci prostředků, která byla přidána v kroku 1. V části **Delegovaná oprávnění**vyberte **možnost Access [resource_name]**. Tato možnost uděluje webové aplikaci oprávnění k vytváření přístupových tokenů, které přistupují k aplikaci prostředků. Udělejte to pro místní i nasazenou verzi webové aplikace, pokud vyvíjíte s Visual Studio a webovou aplikací Azure.

JWT vydané Azure AD je přístupový token slouží k přístupu k prostředku ukazatele.

### <a name="what-about-live-streaming"></a>A co živé vysílání?
Předchozí diskuse se zaměřila na aktiva na vyžádání. A co živé vysílání?

Můžete použít přesně stejný návrh a implementaci k ochraně živého streamování ve službě Media Services tím, že s datovým zdrojem přidruženým k programu budete přistupovat jako k prostředku VOD.

Konkrétně chcete-li provést živé vysílání ve službě Media Services, musíte vytvořit kanál a potom vytvořit program pod kanálem. Chcete-li vytvořit program, musíte vytvořit datový zdroj, který obsahuje živý archiv programu. Chcete-li společnosti CENC poskytnout ochranu živého obsahu s více DRM, použijte stejné nastavení/zpracování na datový zdroj, jako by se jednalo o prostředek VOD před spuštěním programu.

### <a name="what-about-license-servers-outside-media-services"></a>A co licenční servery mimo službu Media Services?
Zákazníci často investovali do licenční serverové farmy buď ve vlastním datovém centru, nebo do farmy hostované poskytovateli služeb DRM. S ochranou obsahu mediálních služeb můžete pracovat v hybridním režimu. Obsah může být hostován a dynamicky chráněn v mediálníslužbě, zatímco licence DRM jsou dodávány servery mimo službu Media Services. V takovém případě zvažte následující změny:

* SLUŽBA STS musí vydávat tokeny, které jsou přijatelné a mohou být ověřeny farmou licenčního serveru. Například licenční servery Widevine poskytované společností Axinom vyžadují konkrétní JWT, který obsahuje zprávu o nároku. Proto musíte mít STS vydat takový JWT. Informace o tomto typu implementace najdete v [Dokumentačním centru Azure](https://azure.microsoft.com/documentation/) a najdete v článku [Použití axinomu k doručování licencí Widevine do Služby Azure Media Services](media-services-axinom-integration.md).
* Již není nutné konfigurovat službu doručování licencí (ContentKeyAuthorizationPolicy) v mediálních službách. Při konfiguraci AssetDeliveryPolicy pro nastavení CENC s multi-DRM je třeba zadat adresy URL pro získání licence (pro PlayReady, Widevine a FairPlay).

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Co když chci použít vlastní STS?
Zákazník se může rozhodnout použít vlastní STS k poskytování JWTs. Důvody zahrnují:

* IDP používané zákazníkem nepodporuje STS. V tomto případě vlastní STS může být možnost.
* Zákazník může potřebovat flexibilnější nebo přísnější kontrolu pro integraci STS s fakturačním systémem odběratele zákazníka. Operátor MVPD může například nabízet více balíčků předplatitelů OTT, jako je například premium, basic a sports. Operátor může chtít porovnat deklarace identity v tokenu s balíčkem odběratele tak, aby byly k dispozici pouze obsah v určitém balíčku. V tomto případě vlastní STS poskytuje potřebnou flexibilitu a kontrolu.

Při použití vlastní STS, musí být provedeny dvě změny:

* Při konfiguraci služby doručování licencí pro datový zdroj, musíte zadat klíč zabezpečení, který se používá pro ověření vlastní STS namísto aktuálníklíč z Azure AD. (Další podrobnosti následují.) 
* Při generování tokenu JTW je určen klíč zabezpečení namísto privátního klíče aktuálního certifikátu X509 ve službě Azure AD.

Existují dva typy klíčů zabezpečení:

* Symetrický klíč: Stejný klíč se používá ke generování a ověření JWT.
* Asymetrický klíč: Pár veřejného a soukromého klíče v certifikátu X509 se používá s privátním klíčem k šifrování/generování JWT a s veřejným klíčem k ověření tokenu.

> [!NOTE]
> Pokud používáte rozhraní .NET Framework/C# jako vývojovou platformu, certifikát X509 použitý pro asymetrický klíč zabezpečení musí mít délku klíče alespoň 2048. Toto je požadavek třídy System.IdentityModel.Tokens.X509AsymmetricSecuritySecurityKey v rozhraní .NET Framework. V opačném případě je vyvolána následující výjimka:
> 
> IDX10630: System.IdentityModel.Tokens.X509AsymmetrIcSecurityKey pro podepisování nemůže být menší než '2048' bitů.

## <a name="the-completed-system-and-test"></a>Dokončený systém a test
V této části naleznete následující scénáře v dokončeném systému začátku do konce, abyste měli před získáním přihlašovacího účtu základní obrázek o chování:

* Pokud potřebujete neintegrovaný scénář:

    * U datových zdrojů videa hostovaných v mediálních službách, které jsou nechráněné nebo chráněné pomocí služby DRM, ale bez ověřování tokenu (vydávání licence tomu, kdo o to požádal), je můžete otestovat bez přihlášení. Přepněte na protokol HTTP, pokud je streamování videa přes protokol HTTP.

* Pokud potřebujete komplexní integrovaný scénář:

    * Pro video prostředky v rámci dynamické ochrany DRM v Media Services, s ověřování tokenu a JWT generované Azure AD, je třeba se přihlásit.

Webová aplikace přehrávače a její přihlášení naleznete na [této webové stránce](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Přihlášení uživatele
Chcete-li otestovat komplexní integrovaný systém DRM, musíte mít vytvořený nebo přidaný účet.

Jaký účet?

Přestože Azure původně povolil přístup jenom uživatelům účtu Microsoft, přístup teď povolují uživatelé z obou systémů. Všechny vlastnosti Azure teď důvěřují Azure AD pro ověřování a Azure AD ověřuje uživatele organizace. Byl vytvořen federační vztah, ve kterém Azure AD důvěřuje systému identit y účtu Microsoft k ověřování uživatelů. V důsledku toho Azure AD můžete ověřit host a účty Microsoft, stejně jako nativní účty Azure AD.

Vzhledem k tomu, že Azure AD důvěřuje doméně účtu Microsoft, můžete přidat všechny účty z některé z následujících domén do vlastního klienta Azure AD a použít účet k přihlášení:

| **Název domény** | **Domény** |
| --- | --- |
| **Vlastní doména klienta Azure AD** |somename.onmicrosoft.com |
| **Podniková doména** |microsoft.com |
| **Doména účtu Microsoft** |outlook.com, live.com, hotmail.com |

Můžete se obrátit na některého z autorů, aby vám vytvořili nebo přidali účet.

Následující snímky obrazovky zobrazují různé přihlašovací stránky používané různými účty domény:

**Vlastní účet domény klienta Azure AD**: Přizpůsobená přihlašovací stránka vlastní domény klienta Azure AD.

![Účet domény vlastního klienta Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Účet domény Microsoft s čipovou kartou**: Přihlašovací stránka přizpůsobená podnikovým IT oddělením společnosti Microsoft s dvoufaktorovým ověřováním.

![Účet domény vlastního klienta Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: Přihlašovací stránka účtu Microsoft pro spotřebitele.

![Účet domény vlastního klienta Azure AD](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití rozšíření šifrovaných médií pro playready
V moderním prohlížeči s rozšířeními encrypted Media Extensions (EME) pro podporu PlayReady, jako je internet explorer 11 ve Windows 8.1 nebo novějším a prohlížeč Microsoft Edge v systému Windows 10, je PlayReady základní technologií DRM pro EME.

![Použití EME pro PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Oblast tmavého přehrávače je proto, že ochrana PlayReady zabraňuje zachycení chráněného videa.

Následující snímek obrazovky ukazuje moduly plug-in přehrávače a podporu Microsoft Security Essentials (MSE)/EME:

![Zásuvné moduly přehrávače pro PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME v Microsoft Edge a Internet Explorer 11 v systému Windows 10 umožňuje [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) být vyvolána na zařízeních se systémem Windows 10, které ji podporují. Hra PlayReady SL3000 odemkne tok vylepšeného prémiového obsahu (4K, HDR) a nových modelů doručování obsahu (pro rozšířený obsah).

Chcete-li se zaměřit na zařízení se systémem Windows, playready je pouze DRM v hardwaru k dispozici na zařízeních se systémem Windows (PlayReady SL3000). Streamovací služba může používat PlayReady prostřednictvím EME nebo prostřednictvím aplikace univerzální platformy Windows a nabídnout vyšší kvalitu videa pomocí PlayReady SL3000 než jiný DRM. Obsah až do velikosti 2 K protéká prohlížečem Chrome nebo Firefox a obsah až do velikosti 4 K protéká přes Microsoft Edge/Internet Explorer 11 nebo aplikaci univerzální platformy Windows na stejném zařízení. Částka závisí na nastavení služby a implementaci.

#### <a name="use-eme-for-widevine"></a>Použití EME pro Widevine
V moderním prohlížeči s podporou EME /Widevine, jako je Chrome 41 + v systému Windows 10, Windows 8.1, Mac OSX Yosemite a Chrome v systému Android 4.4.4, je Google Widevine DRM za EME.

![Použití EME pro Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nebrání tomu, abyste zachytili obrazovku chráněného videa.

![Hráč plug-iny pro Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Uživatelé bez oprávnění
Pokud uživatel není členem skupiny Oprávnění uživatelé, neprojde kontrolou nároků. Služba multi-DRM licence pak odmítne vydat požadovanou licenci, jak je znázorněno. Podrobný popis je "Získání licence se nezdařilo", což je tak, jak bylo navrženo.

![Uživatelé bez oprávnění](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění vlastní služby tokenů zabezpečení
Pokud spustíte vlastní STS, JWT je vydán vlastní STS pomocí symetrické nebo asymetrické klíč.

Následující snímek obrazovky ukazuje scénář, který používá symetrický klíč (pomocí Chromu):

![Vlastní STS se symetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který používá asymetrický klíč prostřednictvím certifikátu X509 (pomocí moderního prohlížeče Microsoft):

![Vlastní STS s asymetrickým klíčem](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

V obou předchozích případech zůstává ověřování uživatelů stejné. Probíhá prostřednictvím Azure AD. Jediný rozdíl je, že JWTs jsou vydávány vlastní STS namísto Azure AD. Při konfiguraci dynamické ochrany CENC určuje omezení služby doručování licencí typ jwt, symetrický nebo asymetrický klíč.

## <a name="summary"></a>Souhrn

Tento dokument popisoval CENC s vícenativnídružidnou drm a řízení přístupu prostřednictvím ověřování tokenů, jeho návrhu a jeho implementace pomocí Azure, Mediálních služeb a Media Playeru.

* Byl předložen referenční návrh, který obsahuje všechny potřebné součásti subsystému DRM/CENC.
* Referenční implementace byla prezentována v Azure, Media Services a Media Player.
* Diskutována byla i některá témata přímo zapojená do návrhu a realizace.

## <a name="additional-notes"></a>Další poznámky

* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
