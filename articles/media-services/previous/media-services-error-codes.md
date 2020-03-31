---
title: Kódy chyb Azure Media Services | Dokumenty společnosti Microsoft
description: V závislosti na problémech, jako jsou například vypršení platnosti ověřovacích tokenů, se mohou od služby zobrazit kódy chyb HTTP, které nejsou podporovány ve službě Media Services. Tento článek poskytuje přehled chybových kódů rozhraní API Azure Media Services v2.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887063"
---
# <a name="azure-media-services-error-codes"></a>Kódy chyb Azure Media Services
Při použití Služby Microsoft Azure Media Services se mohou od služby zobrazit kódy chyb HTTP v závislosti na problémech, jako jsou ověřovací tokeny, jejichž platnost vyprší, na akce, které nejsou ve službě Media Services podporovány. Následuje seznam **kódů chyb PROTOKOLU HTTP,** které mohou být vráceny službou Media Services, a možné příčiny.  

## <a name="400-bad-request"></a>400 Chybný požadavek
Žádost obsahuje neplatné informace a je odmítnuta z jednoho z následujících důvodů:

* Je zadána nepodporovaná verze rozhraní API. Nejnovější verzi naleznete v [tématu Setup for Media Services REST API Development](media-services-rest-how-to-use.md).
* Není zadána verze rozhraní API služby Media Services. Informace o tom, jak určit verzi rozhraní API, naleznete v tématu [Odkaz na rozhraní REST ROZHRANÍ MEDIA Services Operations .](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
  
  > [!NOTE]
  > Pokud používáte sady .NET nebo Java SDK pro připojení ke službě Media Services, je pro vás při každém pokusu a provedení nějaké akce proti službě Media Services určena verze rozhraní API.
  > 
  > 
* Byla zadána nedefinovaná vlastnost. Název vlastnosti je v chybové zprávě. Lze zadat pouze vlastnosti, které jsou členy dané entity. Seznam entit a jejich vlastností najdete [v tématu Odkaz na rozhraní REST API služby Azure Media Services.](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Byla zadána neplatná hodnota vlastnosti. Název vlastnosti je v chybové zprávě. Platné typy vlastností a jejich hodnoty naleznete v předchozím odkazu.
* Chybí hodnota vlastnosti a je vyžadována.
* Část zadané adresy URL obsahuje chybnou hodnotu.
* Došlo k pokusu o aktualizaci vlastnosti WriteOnce.
* Došlo k pokusu o vytvoření úlohy, která má vstupní majetek s primárním souborem AssetFile, který nebyl zadán nebo jej nebylo možné určit.
* Došlo k pokusu o aktualizaci lokátoru SAS. Lokátory SAS lze pouze vytvořit nebo odstranit. Streamovací lokátory lze aktualizovat. Další informace naleznete [v tématu Locators](https://docs.microsoft.com/rest/api/media/operations/locator).
* Byla odeslána nepodporovaná operace nebo dotaz.

## <a name="401-unauthorized"></a>401 Neautorizováno
Požadavek nelze ověřit (před jeho autorizací) z jednoho z následujících důvodů:

* Chybí hlavička ověřování.
* Chybná hodnota hlavičky ověřování.
  * Platnost tokenu vypršela. 
  * Token obsahuje neplatný podpis.

## <a name="403-forbidden"></a>403 Zakázáno
Žádost není povolena z jednoho z následujících důvodů:

* Účet služby Media Services nebyl nalezen nebo byl odstraněn.
* Účet Mediální služby je zakázán a typ požadavku není HTTP GET. Servisní operace vrátí odpověď 403 také.
* Ověřovací token neobsahuje informace o pověření uživatele: AccountName a/nebo SubscriptionId. Tyto informace najdete v rozšíření o používání práva mediálních služeb pro váš účet Media Services na portálu pro správu Azure.
* K prostředku nelze získat přístup.
  
  * Došlo k pokusu o použití mediálního procesoru, který není k dispozici pro váš účet mediálních služeb.
  * Došlo k pokusu o aktualizaci šablony jobtemplate definované službou Media Services.
  * Došlo k pokusu o přepsání lokátoru účtu Media Services.
  * Došlo k pokusu o přepsání obsahu některého jiného účtu Mediální služby.
* Prostředek nelze vytvořit z důvodu kvóty služby, která byla dosažena pro účet Mediálních služeb. Další informace o kvótách služeb naleznete v [tématu Kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Nenalezeno
Požadavek není u prostředku povolen z jednoho z následujících důvodů:

* Došlo k pokusu o aktualizaci entity, která neexistuje.
* Došlo k pokusu o odstranění entity, která neexistuje.
* Došlo k pokusu o vytvoření entity, která odkazuje na entitu, která neexistuje.
* Došlo k pokusu o získání entity, která neexistuje.
* Došlo k pokusu o určení účtu úložiště, který není přidružen k účtu Mediální ch služeb.  

## <a name="409-conflict"></a>409 Konflikt
Žádost není povolena z jednoho z následujících důvodů:

* Více než jeden AssetFile má zadaný název v rámci asset.
* Došlo k pokusu o vytvoření druhého primárního souboru AssetFile v rámci datového zdroje.
* Došlo k pokusu o vytvoření contentkey se zadaným Id již použit.
* Došlo k pokusu o vytvoření lokátoru se zadaným Id, které bylo již použito.
* Více než jeden IngestManifestFile má zadaný název v rámci IngestManifest.
* Došlo k pokusu o propojení druhého šifrování úložiště ContentKey s úložištěm šifrované asset.
* Došlo k pokusu o propojení stejného obsahu s datovým zdrojem.
* Došlo k pokusu o vytvoření lokátoru k datovému zdroji, jehož kontejner úložiště chybí nebo již není přidružen k prostředku.
* Došlo k pokusu o vytvoření lokátoru k assetu, který již používá 5 lokátorů. (Azure Storage vynucuje limit pěti zásad sdíleného přístupu v jednom kontejneru úložiště.)
* Propojení účtu úložiště asset u IngestManifestAsset není stejný jako účet úložiště používaný nadřazený IngestManifest.  

## <a name="500-internal-server-error"></a>500 – Interní chyba serveru
Během zpracování požadavku media services narazí na některé chyby, které brání zpracování pokračovat. Může to být způsobené jedním z následujících důvodů:

* Vytvoření datového zdroje nebo úlohy se nezdaří, protože informace o kvótě služeb účtu Mediální služby jsou dočasně nedostupné.
* Vytvoření asset nebo IngestManifest kontejneru úložiště objektu blob nezdaří, protože informace o účtu účtu účtu je dočasně nedostupná.
* Další neočekávaná chyba.

## <a name="503-service-unavailable"></a>503 – Nedostupná služba
Server nyní nemůže přijímat požadavky. Tato chyba může být způsobena nadměrným požadavky na službu. Mechanismus omezení mediálních služeb omezuje využití prostředků pro aplikace, které poskytují nadměrné požadavky na službu.

> [!NOTE]
> Zkontrolujte chybovou zprávu a řetězec kódu chyby, abyste získali podrobnější informace o důvodu, proč se chyba 503. Tato chyba nemusí vždy znamenat omezení.
> 
> 

Možné popisy stavu jsou:

* "Server je zaneprázdněn. Předchozí spuštění tohoto typu požadavku {0} trvalo více než sekund."
* "Server je zaneprázdněn. Více {0} než požadavky za sekundu lze omezit."
* "Server je zaneprázdněn. Více {0} než požadavky {1} během několika sekund lze omezit."

Chcete-li tuto chybu zpracovat, doporučujeme použít exponenciální back-off logiku opakování. To znamená, že použití postupně delší čeká mezi opakování pro následné chybové odpovědi.  Další informace naleznete [v tématu Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Pokud používáte [Azure Media Services SDK pro .Net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logika opakování pro 503 chyba byla implementována sadou SDK.  
> 
> 

## <a name="see-also"></a>Viz také
[Chybové kódy správy mediálních služeb](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

