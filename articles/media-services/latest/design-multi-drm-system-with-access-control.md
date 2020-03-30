---
title: Systém ochrany obsahu s více DRM – Mediální služby Azure v3
description: Tento článek obsahuje podrobný popis, jak navrhnout systém ochrany obsahu s více DRM pomocí Služby Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161779"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu 

Navrhování a vytváření subsystému drm (Digital Rights Management) pro řešení over-the-top (OTT) nebo online streaming je složitý úkol. Operátoři/poskytovatelé online videa obvykle zadávají tento úkol specializovaným poskytovatelům služeb DRM. Cílem tohoto dokumentu je představit referenční návrh a referenční implementaci komplexního subsystému DRM v OTT nebo online streamovacím řešení.

Cílené čtečky pro tento dokument jsou inženýři, kteří pracují v subsystémech DRM OTT nebo online streaming / multiscreen řešení nebo čtenáři, kteří se zajímají o subsystémy DRM. Předpokládá se, že čtenáři jsou obeznámeni alespoň s jednou z technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe Access.

V této diskusi pomocí multi-DRM zahrneme 3 DRM podporované Mediální služby Azure: Společné šifrování (CENC) pro PlayReady a Widevine, FairPlay a AES-128 jasné šifrování klíče. Hlavním trendem v on-line streamingu a OTT průmyslu je použití nativních DRM na různých klientských platformách. Tento trend je posun od předchozího, který používal jeden DRM a jeho klientsdka pro různé klientské platformy. Při použití CENC s multi-nativní DRM, playready a Widevine jsou šifrovány podle [specifikace společné šifrování (ISO/IEC 23001-7 CENC).](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/)

Výhody použití nativní multi-DRM pro ochranu obsahu je, že:

* Snižuje náklady na šifrování, protože jeden proces šifrování se používá k cílení na různé platformy s jeho nativní DRM.
* Snižuje náklady na správu prostředků, protože v úložišti je potřeba pouze jedna kopie datového zdroje.
* Eliminuje náklady na licencování klientů DRM, protože nativní klient DRM je obvykle zdarma na své nativní platformě.

### <a name="goals-of-the-article"></a>Cíle článku

Cílem tohoto článku je:

* Poskytněte referenční návrh subsystému DRM, který používá všechny 3 DRM (CENC pro DASH, FairPlay pro HLS a PlayReady pro plynulé streamování).
* Poskytněte referenční implementaci na platformě Azure a Azure Media Services.
* Diskutujte o některých tématech návrhu a implementace.

Následující tabulka shrnuje nativní podporu DRM na různých platformách a eme podporu v různých prohlížečích.

| **Platforma klienta** | **Nativní DRM** | **Eme** |
| --- | --- | --- |
| **Chytré televizory, STE** | PlayReady, Widevine a/nebo jiné | Vestavěný prohlížeč/EME pro PlayReady a/nebo Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 pro PlayReady|
| **Zařízení se systémem Android (telefon, tablet, TV)** |Widevine |Chrom pro Widevine |
| **iOS** | FairPlay | Safari pro FairPlay (od iOS 11.2) |
| **Macos** | FairPlay | Safari pro FairPlay (od Safari 9 + na Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Vzhledem k aktuálnímu stavu nasazení pro každý DRM, služba obvykle chce implementovat dva nebo tři DRM a ujistěte se, že adresu všech typů koncových bodů v nejlepším způsobem.

Existuje kompromis mezi složitostí logiky služby a složitostí na straně klienta k dosažení určité úrovně uživatelského prostředí u různých klientů.

Chcete-li provést výběr, mějte na paměti:

* PlayReady je nativně implementován v každém zařízení se systémem Windows, na některých zařízeních Android a je k dispozici prostřednictvím softwarových sad SDK prakticky na libovolné platformě.
* Widevine je nativně implementována v každém zařízení Android, v Chromu a v některých dalších zařízeních. Widevine je také podporován v prohlížečích Firefox a Opera přes DASH.
* FairPlay je k dispozici na iOS, macOS a tvOS.


## <a name="a-reference-design"></a>Referenční návrh
Tato část představuje referenční návrh, který je agnostik k technologiím používaným k jeho implementaci.

Subsystém DRM může obsahovat následující součásti:

* Správa klíčů
* Balení šifrování DRM
* Doručování licencí DRM
* Kontrola oprávnění/kontrola přístupu
* Ověření/autorizace uživatele
* Aplikace pro hráče
* Síť pro doručování původu/obsahu (CDN)

Následující diagram znázorňuje interakci na vysoké úrovni mezi součástmi v subsystému DRM:

![Drm subsystém s CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Služba zabezpečeného tokenu (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Dynamická ochrana Mediálních služeb Azure |
| **Doručování licencí DRM** |* Poskytování licencí media services (PlayReady, Widevine, FairPlay) <br/>* Licenční server Axinom <br/>* Vlastní PlayReady licenční server |
| **Zdroj** |Koncový bod streamování služby Azure Media Services |
| **Správa klíčů** |Není potřeba pro referenční implementaci |
| **Správa obsahu** |Konzolová aplikace Jazyka C# |

Jinými slovy, idp a STS jsou poskytovány Azure AD. Rozhraní [API přehrávače médií Azure](https://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Azure Media Services i Azure Media Player podporují CENC přes DASH, FairPlay přes HLS, PlayReady přes plynulé streamování a šifrování AES-128 pro DASH, HLS a hladké.

Následující diagram znázorňuje celkovou strukturu a tok s předchozím mapováním technologií:

![CENC o mediálních službách](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Chcete-li nastavit ochranu obsahu DRM, nástroj pro správu obsahu používá následující vstupy:

* Otevřený obsah
* Klíč obsahu ze správy klíčů
* Adresy URL získání licencí
* Seznam informací z Azure AD, jako je například cílová skupina uživatelů, vystavite a deklarace tokenu

Zde je výstup nástroje pro správu obsahu:

* ContentKeyPolicy popisuje šablonu licence DRM pro každý použitý druh drm;
* ContentKeyPolicyRestriction popisuje řízení přístupu před vydáním licence DRM
* StreamingPolicy popisuje různé kombinace DRM - režim šifrování - streaming protokol - formát kontejneru, pro streamování
* StreamingLocator popisuje klíč obsahu/IV používaný pro šifrování a streamování adres URL 

Zde je tok za běhu:

* Při ověření uživatele je generován jwt.
* Jeden z deklarací obsažených v JWT je deklarace skupiny, která obsahuje ID objektu skupiny EntitledUserGroup. Toto tvrzení se používá k propustku kontrolou nároků.
* Přehrávač stáhne klientský manifest obsahu chráněného cenc a identifikuje následující:
   * ID klíče.
   * Obsah je chráněn proti drm.
   * Adresy URL získání licencí.
* Přehrávač provede požadavek na získání licence na základě podporovaného prohlížeče/DRM. V žádosti o získání licence jsou také odeslány ID klíče a JWT. Služba doručování licencí ověří JWT a nároky obsažené před vydáním potřebné licence.

## <a name="implementation"></a>Implementace
### <a name="implementation-procedures"></a>Prováděcí postupy
Implementace zahrnuje následující kroky:

1. Připravte testovací prostředky. Zakódovat/zabalit testovací video do fragmentovaného protokolu MP4 s více přenosovou rychlostí ve službě Media Services. Tento prostředek *není* chráněn driody. Ochrana DRM se provádí pomocí dynamické ochrany později.

2. Vytvořte ID klíče a klíč obsahu (volitelně z osiva klíče). V tomto případě není potřeba systém správy klíčů, protože pro několik testovacích prostředků je vyžadováno pouze jedno ID klíče klíče klíče a klíč obsahu.

3. Pomocí rozhraní API mediálních služeb nakonfigurujte služby doručování licencí více DRM pro testovací datový zdroj. Pokud používáte vlastní licenční servery vaší společností nebo dodavateli vaší společnosti namísto licenčních služeb v aplikaci Media Services, můžete tento krok přeskočit. Při konfiguraci doručování licencí můžete v kroku zadat adresy URL pro získání licencí. Rozhraní API mediálních služeb je potřeba k určení některých podrobných konfigurací, jako je například omezení zásad autorizace a šablony odpovědí na licence pro různé licenční služby DRM. V tuto chvíli portál Azure neposkytuje potřebné ui pro tuto konfiguraci. Informace na úrovni rozhraní API a ukázkový kód naleznete [v tématu Použití běžného dynamického šifrování PlayReady a/nebo Widevine](protect-with-drm.md).

4. Pomocí rozhraní API mediálních služeb nakonfigurujte zásady doručování datových zdrojů pro testovací datový zdroj. Informace na úrovni rozhraní API a ukázkový kód naleznete [v tématu Použití běžného dynamického šifrování PlayReady a/nebo Widevine](protect-with-drm.md).

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

* Informace o vývojáři najdete v [průvodci vývojářem služby Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informace o správci najdete v [adresáři klienta Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Některé otázky při provádění

Nápovědu k problémům s implementací použijte následující informace o řešení potíží.

* Adresa URL vystavittele musí končit "/". Cílová skupina musí být ID klienta aplikace přehrávače. Také přidejte "/" na konci adresy URL vystavittele.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    V [Dekodéru JWT](http://jwt.calebb.net/)vidíte **aud** a **iss**, jak je znázorněno na JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Přidejte oprávnění k aplikaci ve službě Azure AD na kartě **Konfigurovat** aplikace. Oprávnění jsou vyžadována pro každou aplikaci, místní i nasazené verze.

    ![Oprávnění](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

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

![Účet domény vlastního klienta Azure AD jeden](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Účet domény Microsoft s čipovou kartou**: Přihlašovací stránka přizpůsobená podnikovým IT oddělením společnosti Microsoft s dvoufaktorovým ověřováním.

![Účet domény vlastního klienta Azure AD dva](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: Přihlašovací stránka účtu Microsoft pro spotřebitele.

![Účet domény vlastního klienta Azure AD tři](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití rozšíření šifrovaných médií pro playready

V moderním prohlížeči s rozšířeními encrypted Media Extensions (EME) pro podporu PlayReady, jako je internet explorer 11 ve Windows 8.1 nebo novějším a prohlížeč Microsoft Edge v systému Windows 10, je PlayReady základní technologií DRM pro EME.

![Použití EME pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Oblast tmavého přehrávače je proto, že ochrana PlayReady zabraňuje zachycení chráněného videa.

Následující snímek obrazovky ukazuje moduly plug-in přehrávače a podporu Microsoft Security Essentials (MSE)/EME:

![Zásuvné moduly přehrávače pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME v Microsoft Edge a Internet Explorer 11 v systému Windows 10 umožňuje [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) být vyvolána na zařízeních se systémem Windows 10, které ji podporují. Hra PlayReady SL3000 odemkne tok vylepšeného prémiového obsahu (4K, HDR) a nových modelů doručování obsahu (pro rozšířený obsah).

Chcete-li se zaměřit na zařízení se systémem Windows, playready je pouze DRM v hardwaru k dispozici na zařízeních se systémem Windows (PlayReady SL3000). Streamovací služba může používat PlayReady prostřednictvím EME nebo prostřednictvím aplikace univerzální platformy Windows a nabídnout vyšší kvalitu videa pomocí PlayReady SL3000 než jiný DRM. Obsah až do velikosti 2 K protéká prohlížečem Chrome nebo Firefox a obsah až do velikosti 4 K protéká přes Microsoft Edge/Internet Explorer 11 nebo aplikaci univerzální platformy Windows na stejném zařízení. Částka závisí na nastavení služby a implementaci.

#### <a name="use-eme-for-widevine"></a>Použití EME pro Widevine

V moderním prohlížeči s podporou EME /Widevine, jako je Chrome 41 + v systému Windows 10, Windows 8.1, Mac OSX Yosemite a Chrome v systému Android 4.4.4, je Google Widevine DRM za EME.

![Použití EME pro Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nebrání tomu, abyste zachytili obrazovku chráněného videa.

![Hráč plug-iny pro Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Použijte EME pro FairPlay

Podobně můžete testovat chráněný obsah FairPlay v tomto testovacím přehrávači v Safari na macOS nebo iOS 11.2 a novějších.

Ujistěte se, že jste "FairPlay" jako protectionInfo.type a dát do správné URL pro váš certifikát aplikace v FPS AC Path (FairPlay Streaming aplikace Cesta certifikátu).

### <a name="unentitled-users"></a>Uživatelé bez oprávnění

Pokud uživatel není členem skupiny Oprávnění uživatelé, neprojde kontrolou nároků. Služba multi-DRM licence pak odmítne vydat požadovanou licenci, jak je znázorněno. Podrobný popis je "Získání licence se nezdařilo", což je tak, jak bylo navrženo.

![Uživatelé bez oprávnění](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Spuštění vlastní služby tokenů zabezpečení

Pokud spustíte vlastní STS, JWT je vydán vlastní STS pomocí symetrické nebo asymetrické klíč.

Následující snímek obrazovky ukazuje scénář, který používá symetrický klíč (pomocí Chromu):

![Vlastní STS se symetrickým klíčem](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Následující snímek obrazovky ukazuje scénář, který používá asymetrický klíč prostřednictvím certifikátu X509 (pomocí moderního prohlížeče Microsoft):

![Vlastní STS s asymetrickým klíčem](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

V obou předchozích případech zůstává ověřování uživatelů stejné. Probíhá prostřednictvím Azure AD. Jediný rozdíl je, že JWTs jsou vydávány vlastní STS namísto Azure AD. Při konfiguraci dynamické ochrany CENC určuje omezení služby doručování licencí typ jwt, symetrický nebo asymetrický klíč.

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy](frequently-asked-questions.md)
* [Přehled ochrany obsahu](content-protection-overview.md)
* [Chraňte svůj obsah pomocí DRM](protect-with-drm.md)
