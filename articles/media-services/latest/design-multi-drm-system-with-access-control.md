---
title: Systém ochrany obsahu s více DRM – Azure Media Services V3
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
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161779"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Návrh systému s více variantami DRM ochrany obsahu pomocí řízení přístupu 

Návrh a vytváření správy digitálních práv (DRM) subsystém pro over-the-top (OTT) nebo řešení online streamování je složitý úkol. Operátory/online video poskytovatelé obvykle externí pomocí této úlohy můžete specializované DRM poskytovatelů služeb. Cílem tohoto dokumentu je k dispozici referenční návrh a referenční implementace subsystému DRM začátku do konce v OTT nebo řešení online streamování.

Cílové čtecí zařízení pro tento dokument se techniků, kteří pracují v DRM subsystémy OTT nebo řešení online streamování/s více obrazovkami nebo čtenáře, kteří mají zájem o subsystémy DRM. Předpokladem je, že čtečky obeznámeni s alespoň jedním technologií DRM na trhu, jako je PlayReady, Widevine, FairPlay nebo Adobe přístup.

V této diskuzi pomocí několika variant DRM zahrnujeme 3 technologiemi DRM podporovaných službou Azure Media Services: běžné šifrování (CENC) PlayReady a Widevine, FairPlay, jakož i AES-128 s nezašifrovaným klíčem. Hlavní trendu v oboru OTT a online streamování se má používat nativní technologiemi DRM na různých klientských platformách. Tento trend je posun z předchozí, který používá jeden DRM a jeho Klientská sada SDK pro různé platformy klienta. Pokud používáte CENC s více nativními technologiemi DRM, jsou protokoly PlayReady i Widevine šifrované podle specifikace [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

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

| **Klientská platforma** | **Nativní DRM** | **EME** |
| --- | --- | --- |
| **Inteligentní televizory, STBs** | PlayReady, Widevine, nebo jiné | Vložený prohlížeče/EME PlayReady nebo Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 pro PlayReady|
| **Zařízení s Androidem (telefon, tablet, TV)** |Widevine |Chrome pro Widevine |
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

| **ContentKey na Asset** | **Scénář** |
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

| **Stavební blok** | **Technologie** |
| --- | --- |
| **Přehrávač** |[Přehrávač médií Azure](https://azure.microsoft.com/services/media-services/media-player/) |
| **Zprostředkovatel identity (IDP)** |Azure Active Directory (Azure AD) |
| **Služba tokenů zabezpečení (STS)** |Azure AD |
| **Pracovní postup ochrany DRM** |Azure Media Services dynamické ochrany |
| **Doručování licencí DRM** |* Naším vlastním doručováním licencí Media Services (PlayReady, Widevine, FairPlay) <br/>* Axinom licenčního serveru <br/>* Vlastní serverové licence PlayReady |
| **Zdroji** |Koncový bod streamování Azure Media Services |
| **Správa klíčů** |Nevyžaduje se pro referenční implementace |
| **Správa obsahu** |Konzolová aplikace jazyka C# |

Jinými slovy zprostředkovatele identity a služba tokenů zabezpečení jsou k dispozici ve službě Azure AD. [Rozhraní API pro Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) se používá pro přehrávač. Azure Media Services i Azure Media Player podporují CENC přes POMLČKu, FairPlay nad HLS, PlayReady přes hladké streamování a šifrování AES-128 pro POMLČKy, HLS a hladké.

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

1. Připravte testovací prostředky. Balení/kódování videa s více přenosovými rychlostmi test fragmentovaného MP4 ve službě Media Services. Tento *prostředek není chráněn technologií DRM.* Ochrana DRM provádí dynamické ochrany později.

2. Vytvořte klíč ID a klíč k obsahu (volitelně z klíče počáteční hodnota). V takovém případě není nutné systémem správy klíčů, protože pouze jeden klíč ID a klíč k obsahu, které jsou požadovány pro několik prostředků testu.

3. Rozhraní API služby Media Services použijte ke konfiguraci služeb doručování licence s více variantami DRM pro testovací prostředek. Pokud používáte vlastní licenční servery ve vaší společnosti nebo vaše společnost dodavatelů místo licenční služby Media Services, můžete tento krok přeskočit. Můžete zadat licenci získání adresy URL v kroku při konfiguraci naším vlastním doručováním licencí. Rozhraní API služby Media Services je potřeba zadat některé podrobné konfigurace, jako je například omezení zásad autorizace a licence odpovědi šablony pro různé služby licence DRM. V tuto chvíli na webu Azure portal neposkytuje rozhraní potřebných pro tuto konfiguraci. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](protect-with-drm.md).

4. Konfigurace zásad doručení assetu pro testovací prostředek pomocí rozhraní API služby Media Services. Informace o úrovni rozhraní API a ukázkový kód najdete v tématu [použití dynamického společného šifrování PlayReady nebo Widevine](protect-with-drm.md).

5. Vytvořit a nakonfigurovat tenanta služby Azure AD v Azure.

6. Vytvořte několik uživatelských účtů a skupin ve vašem tenantovi Azure AD. Vytvořte aspoň skupinu "Uživatele s názvem" a přidání uživatele do této skupiny. Uživatelé v této skupině se předá Kontrola nároku získání licence. Není v této skupině uživatelů k předání kontroly ověřování a nelze získat licenci. Členství v této skupině "Uživatele s názvem" je taková deklarace požadovaných skupin v JWT vydaného službě Azure AD. Tento požadavek deklarace identity v kroku zadáte při konfiguraci s více variantami DRM služeb doručování licence.

7. Vytvoření aplikace ASP.NET MVC pro hostování přehrávače videa. Tato aplikace technologie ASP.NET je chráněný pomocí ověření uživatele vůči tenanta Azure AD. Správné deklarace jsou součástí přístupové tokeny získat po ověření uživatele. Pro tento krok doporučujeme OpenID Connect rozhraní API. Nainstalujte následující balíčky NuGet:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Vytvořte přehrávač pomocí [rozhraní Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Pomocí [rozhraní Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) určete, která technologie DRM se má používat na různých platformách DRM.

9. V následující tabulce jsou uvedeny testovací matrice.

    | **DIGITÁLNÍCH** | **Prohlížeee** | **Výsledek pro oprávněného uživatele** | **Výsledek pro neoprávněného uživatele** |
    | --- | --- | --- | --- |
    | **Modul** |Microsoft Edge nebo Internet Explorer 11 ve Windows 10 |Úspěšné |Selhání |
    | **Widevine** |Chrome, Firefox, Opera |Úspěšné |Selhání |
    | **FairPlay** |Safari v systému macOS      |Úspěšné |Selhání |
    | **AES-128** |Většina moderních prohlížečů  |Úspěšné |Selhání |

Informace o tom, jak nastavit Azure AD pro aplikaci aktéra ASP.NET MVC, najdete v tématu [integrace aplikace založené na Azure Media Services Owin MVC s Azure Active Directory a omezení doručování klíčů obsahu na základě DEKLARACÍ JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Další informace najdete v tématu [ověřování tokenu JWT v Azure Media Services a dynamické šifrování](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Informace o službě Azure AD:

* Informace o vývojářích najdete v [příručce pro vývojáře Azure Active Directory](../../active-directory/develop/v2-overview.md).
* Informace o Správci najdete ve [správě adresáře tenanta Azure AD](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Některé problémy v provádění

Použijte následující informace o odstraňování potíží pro pomoc při potížích s implementací.

* Vydavatel, adresa URL musí končit "/". Cílová skupina musí být ID klienta aplikace přehrávače. Přidejte také "/" na konci adresy URL vystavitele.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    V [dekodéru JWT](http://jwt.calebb.net/)vidíte **AUD** a **ISS**, jak je znázorněno v tokenu JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Přidejte do aplikace ve službě Azure AD oprávnění na kartě **Konfigurace** aplikace. Oprávnění se vyžadují pro každou aplikaci, nasazené i místní verze.

    ![Oprávnění](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Při nastavování ochrany dynamické šifrování CENC, použijte správný vystavitele.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    Následující nefunguje:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    Identifikátor GUID je ID tenanta Azure AD. Identifikátor GUID lze nalézt v místní nabídce **koncových bodů** v Azure Portal.

* Členství ve skupině udělit deklarací oprávnění. Ujistěte se, že je v souboru manifestu aplikace Azure AD: 

    "groupMembershipClaims": "Vše" (výchozí hodnota je null)

* Nastavte správný typ TokenType při vytváření omezení požadavků.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Vzhledem k tomu, že přidáte podporu pro token JWT (Azure AD) kromě SWT (ACS), je výchozí typ TokenType TokenType.JWT. Pokud používáte SWT/ACS, je nutné nastavit token k TokenType.SWT.

## <a name="the-completed-system-and-test"></a>Dokončené systému a testování

Tato část vás provede následující scénáře v dokončené začátku do konce systému tak, aby základní přehled o chování může mít, než se ponoříte přihlašovací účet:

* Pokud potřebujete neintegrovaném scénáře:

    * Pro video-prostředků ve službě Media Services, které jsou hostované buď nechráněné nebo chráněné DRM, ale bez ověřování pomocí tokenu (vystavování licenci těmi požadovaná), takže ji můžete otestovat bez přihlášení. Přepnout na HTTP, pokud vaše video datové proudy přes protokol HTTP.

* Pokud je třeba integrovaný scénář začátku do konce:

    * Pro video-prostředků v rámci dynamických DRM ochrany ve službě Media Services pomocí ověření tokenu a JWT vygenerované službou Azure AD budete muset přihlásit.

Pro webovou aplikaci Player a její přihlášení si přečtěte [Tento web](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Přihlášení uživatele
K otestování integrovaného systému DRM začátku do konce, musíte mít účet vytvoření nebo přidání.

Který účet?

I když Azure původně umožňovala přístup pouze uživatelům účtů Microsoft, je nyní povolen přístup uživatelům z obou systémů. Všechny vlastnosti v Azure teď vztah důvěryhodnosti Azure AD pro ověřování a Azure AD ověřuje organizační uživatele. Vztah federace byl vytvořen, kde Azure AD nechává systémem identit uživatelů účtů Microsoft spotřebitelské uživatele ověřovat. V důsledku toho může Azure AD ověřit hostovaný Microsoft Azure AD stejně jako nativní účty.

Protože Azure AD nechává doména účtu Microsoft, můžete přidat všechny účty v kterékoli z následujících domén do vlastní služby Azure AD tenanta a přihlaste se pomocí účtu:

| **Název domény** | **Domain** |
| --- | --- |
| **Vlastní doména tenanta Azure AD** |somename.onmicrosoft.com |
| **Firemní doména** |microsoft.com |
| **Doména účet Microsoft** |Outlook.com, live.com, hotmail.com |

Autoři měli účet vytvoření nebo přidání za vás může kontaktovat.

Na následujících snímcích obrazovky zobrazit různé přihlašovací stránky používá jinou doménu účty:

**Vlastní účet domény tenanta Azure AD**: přizpůsobená přihlašovací stránka vlastní domény TENANTA Azure AD.

![Účtu tenanta domény vlastní služby Azure AD, jeden](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Účet domény Microsoft s čipovou kartou**: přihlašovací stránka přizpůsobená MICROSOFTem IT má dvojúrovňové ověřování.

![Účet domény vlastní služby Azure AD tenanta dvě](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Účet Microsoft**: přihlašovací stránka účet Microsoft pro příjemce.

![Účet domény vlastní služby Azure AD tenanta tři](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Použití rozšíření šifrované média pro PlayReady

Plán PlayReady je v moderním prohlížeči s Media rozšíření eme (Encrypted Extensions) pro podporu PlayReady, jako je například Internet Explorer 11 na Windows 8.1 nebo novější a prohlížeč Microsoft Edge ve Windows 10 v podkladové DRM pro rozšíření EME.

![Použití rozšíření EME PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

Tmavě player oblasti totiž ochranou PlayReady brání v provádění snímek obrazovky videa chráněné.

Následující snímek obrazovky ukazuje player moduly plug-in a Microsoft Security Essentials (MSE) / EME podporují:

![Přehrávač moduly plug-in pro PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

EME na Microsoft Edge a Internet Explorer 11 ve Windows 10 umožňuje, aby se [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) vyvolal na zařízeních s Windows 10, která ho podporují. PlayReady SL3000 odemkne tok obsahu rozšířené úrovně premium (4 kB, HDR) a nový obsah doručování modely (pro rozšířený obsah).

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

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy](frequently-asked-questions.md)
* [Přehled ochrany obsahu](content-protection-overview.md)
* [Chraňte svůj obsah pomocí DRM](protect-with-drm.md)
