---
title: Nejčastější dotazy k Azure Media Services V3 | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy k Azure Media Services V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 28b9c8f343437c20e277d2f3ba53767afa45a5c2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501256"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Nejčastější dotazy k Media Services V3

Tento článek obsahuje odpovědi na nejčastější dotazy k Azure Media Services (AMS) v3.

## <a name="general"></a>Obecné

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Jaké role Azure můžou provádět akce s Azure Media Services prostředky? 

Přečtěte si téma [řízení přístupu na základě role (RBAC) pro účty Media Services](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Návody nakonfigurovat rezervované jednotky médií?

Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

Podrobnosti najdete v tématu [škálování zpracování médií pomocí](media-reserved-units-cli-how-to.md)rozhraní příkazového řádku.

### <a name="what-is-the-recommended-method-to-process-videos"></a>Jaká je doporučená metoda pro zpracování videí?

Pomocí [transformací](https://docs.microsoft.com/rest/api/media/transforms) můžete nakonfigurovat běžné úlohy pro kódování a analýzu videí. Každá **transformace** popisuje recept nebo pracovní postup úloh pro zpracování vašich videosouborů nebo zvukových souborů. [Úloha](https://docs.microsoft.com/rest/api/media/jobs) je skutečný požadavek na Media Services, jak použít transformaci  na dané vstupní video nebo zvukový obsah. Po vytvoření transformace můžete odesílat úlohy pomocí rozhraní Media Services API nebo kterékoli z publikovaných sad SDK. Další informace najdete v tématu [Transformace a úlohy](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Jak funguje stránkování?

Při použití stránkování byste měli vždy použít další odkaz k zobrazení výčtu kolekce a nezáleží na konkrétní velikosti stránky. Podrobnosti a příklady najdete v tématu [filtrování, řazení, stránkování](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Jaké funkce ještě nejsou v Azure Media Services V3 k dispozici?

Podrobnosti najdete v tématu [mezery funkcí v souvislosti s rozhraními API v2](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Jaký je proces přesunutí účtu Media Services mezi předplatnými?  

Podrobnosti najdete v tématu [přesun Media Services účtu mezi](media-services-account-concept.md)předplatnými.

## <a name="live-streaming"></a>Živé streamování 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Jak během živého streamu vkládat přestávky, videa a obrazové položky?

Media Services V3 Live Encoding ještě nepodporuje vkládání videa nebo obrazových obrázků v živém streamu. 

Pro přepnutí zdrojového videa můžete použít [živý místní kodér](recommended-on-premises-live-encoders.md) . Mnoho aplikací nabízí možnost přepnout zdroje, včetně Wirecast streamování, přepínač studia (v iOS), OBS studia (bezplatná aplikace) a spousty dalších.

## <a name="content-protection"></a>Ochrana obsahu

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Mám použít šifrované šifrování klíče AES-128 nebo systém DRM?

Zákazníci často zajímat, zda by měl používat šifrování AES nebo systému DRM. Hlavním rozdílem mezi těmito dvěma systémy je to, že při šifrování AES se klíč obsahu přenáší klientovi přes protokol TLS, aby se klíč zašifroval při přenosu, ale bez dalšího šifrování ("v jasném stavu"). V důsledku toho je klíč, který se používá k dešifrování obsahu, přístupný klientskému přehrávači a je možné ho zobrazit v trasování sítě na klientovi v prostém textu. Šifrování s nezašifrovaným klíčem AES-128 je vhodné pro případy použití, kde se jedná o důvěryhodnou osobu (například k šifrování firemních videí distribuovaných v rámci společnosti, která budou zaměstnanci prohlížet).

Systémy DRM, jako je PlayReady, Widevine a FairPlay, poskytují další úroveň šifrování na klíč, který se používá k dešifrování obsahu v porovnání s jasným klíčem AES-128. Klíč obsahu je zašifrovaný na klíč chráněný modulem runtime DRM v dalších pro jakékoli šifrování na úrovni přenosu poskytované protokolem TLS. Kromě toho se v zabezpečeném prostředí na úrovni operačního systému, kde je obtížnější uživatel se zlými úmysly k útoku na zpracovává dešifrování. DRM se doporučuje pro případy použití, kdy prohlížeč pravděpodobně nebude důvěryhodná strana a vyžadujete nejvyšší úroveň zabezpečení.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Jak a kde se získat token JWT před jeho použitím žádosti o licenci nebo klíč?

1. V produkčním prostředí potřebujete službu tokenů zabezpečení (STS) (webová služba), která vydává token JWT po požadavku HTTPS. Pro testování, můžete použít kód zobrazený v **GetTokenAsync** metody definované v [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Přehrávač, bude nutné vytvořit žádost, po ověření uživatele, na službu STS pro takový token a přiřaďte ji jako hodnotu tokenu. Můžete použít [rozhraní API služby Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

* Příkladem s symetrické i asymetrické klíč služby tokenů zabezpečení, najdete v tématu [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Příklad přehrávač založené na Azure Media Player pomocí těchto tokenu JWT, naleznete v tématu [ https://aka.ms/amtest ](https://aka.ms/amtest) (expand na token vstup odkaz "player_settings").

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Jak autorizujete požadavky na datový proud videa s využitím šifrování AES

Správný přístup je využívat služby tokenů zabezpečení (zabezpečení Token Service):

V závislosti na profilu uživatele přidejte v rámci služby STS různé deklarace identity (například "uživatel Premium", "základní uživatel", "bezplatný zkušební uživatel"). S jinou deklarací v token JWT uživatel uvidí jiný obsah. Samozřejmě pro jiný obsah nebo prostředek, bude mít ContentKeyPolicyRestriction odpovídající RequiredClaims.

Použijte rozhraní API pro Azure Media Services ke konfiguraci poskytování licencí/klíčů a šifrování prostředků (jak je znázorněno v [této ukázce](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Další informace naleznete v tématu:

- [Přehled ochrany obsahu](content-protection-overview.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>Protokol HTTP nebo HTTPS?
Aplikace ASP.NET MVC hráč musí podporovat následující:

* Ověřování uživatelů prostřednictvím Azure AD, která je v rámci protokolu HTTPS.
* Výměna tokenů JWT mezi klientem a Azure AD, která je v rámci protokolu HTTPS.
* Získání licence DRM klientem, který musí být v rámci protokolu HTTPS, pokud je naším vlastním doručováním licencí poskytované službou Media Services. Sada produktů PlayReady není povinné HTTPS pro naším vlastním doručováním licencí. Pokud je server licencí PlayReady mimo Media Services, můžete použít protokol HTTP nebo HTTPS.

Aplikace přehrávače ASP.NET používá protokol HTTPS jako osvědčený postup, takže Media Player je na stránce v části protokol HTTPS. HTTP je však upřednostňované pro streamování, takže je třeba vzít v úvahu problém smíšený obsah.

* Prohlížeč neumožňuje smíšený obsah. Ale mohla moduly plug-in jako Silverlight a modulu plug-in pro OSMF plynulé a pomlčka. Smíšený obsah je kvůli ohrožení schopnost injektovat škodlivý jazyka JavaScript, který může způsobit, že zákaznická data nepředáme být ohrožena zabezpečení. Prohlížeče blokovat tato funkce ve výchozím nastavení. Jediný způsob, jak obejít je na straně serveru (původní) tím, že všechny domény (bez ohledu na HTTPS nebo HTTP). To je pravděpodobně není vhodné.
* Nepoužívejte smíšený obsah. Aplikace přehrávače a Media Player by měl používat protokol HTTP nebo HTTPS. Při přehrávání smíšený obsah, technické silverlightSS zrušením upozornění smíšený obsah. Odborný flashSS zpracovává smíšený obsah bez upozornění smíšený obsah.
* Pokud váš koncový bod streamování byl vytvořen před srpnem 2014, nebudou podporovat protokol HTTPS. V tomto případě vytvořit a použít nový koncový bod streamování pro protokol HTTPS.

### <a name="what-about-live-streaming"></a>A co živé streamování?

K ochraně živého streamování služby Media Services díky tomu asset přidružený k programu jako asset videa na Vyžádání, že můžete přesně stejné návrh a implementaci. Pokud chcete zajistit ochranu pomocí živého obsahu s více technologiemi DRM, použijte stejné nastavení nebo zpracování na Asset, jako by šlo o VOD Asset ještě předtím, než provedete přidružení Assetu s živým výstupem.

### <a name="what-about-license-servers-outside-media-services"></a>A co licenční servery mimo Media Services?

Zákazníci často investovali do serverové farmy licence buď ve své vlastní datové centrum nebo jeden hostitelských počítačích poskytovatelů služeb DRM. Pomocí Media Services content protection můžete provozovat v hybridním režimu. Obsah můžete hostované a dynamicky chráněné ve službě Media Services, zatímco licence DRM jsou poskytované servery mimo Media Services. V takovém případě zvažte následující změny:

* Služba tokenů zabezpečení je potřeba vystavovat tokeny, které jsou přijatelné a dá ověřit pomocí licence serverové farmy. Servery licence Widevine poskytované Axinom například vyžadovat konkrétní token JWT, který obsahuje zprávu o oprávnění. Proto budete muset mít služby STS pro vydávání takový token JWT. 
* Už je potřeba nakonfigurovat službu doručování licencí Media Services. Je potřeba zadat licence získání adresy URL (PlayReady, Widevine a FairPlay) při konfiguraci ContentKeyPolicies.

## <a name="media-services-v2-vs-v3"></a>Media Services V2 vs V3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Můžu Azure Portal použít ke správě prostředků V3?

Aktuálně nemůžete spravovat prostředky v3 pomocí webu Azure Portal. Použijte rozhraní [REST API](https://aka.ms/ams-v3-rest-ref), [rozhraní příkazového řádku](https://aka.ms/ams-v3-cli-ref) nebo některou z podporovaných sad [SDK](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Je v v3 koncept AssetFile?

AssetFiles se odebral z rozhraní API AMS, aby bylo možné oddělit Media Services od závislosti sady SDK úložiště. Teď je úložiště, které není Media Services, uchovává informace, které patří do úložiště. 

Další informace najdete v tématu [migrace na Media Services V3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Kde se nacházelo šifrování úložiště na straně klienta?

Doporučuje se použít šifrování úložiště na straně serveru (což je ve výchozím nastavení zapnuté). Další informace najdete v tématu [šifrování služby Azure Storage pro](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)neaktivní neaktivní data.

## <a name="next-steps"></a>Další postup

[Přehled Media Services V3](media-services-overview.md)
