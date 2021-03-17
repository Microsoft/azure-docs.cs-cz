---
title: Kódy chyb Azure Media Services | Microsoft Docs
description: V závislosti na problémech, jako je vypršení platnosti ověřovacích tokenů k akcím, které nejsou podporované v Media Services, můžete od služby zobrazit chybové kódy HTTP. Tento článek obsahuje přehled kódů chyb rozhraní API pro Azure Media Services V2.
author: IngridAtMicrosoft
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
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013410"
---
# <a name="azure-media-services-error-codes"></a>Kódy chyb služby Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Při použití Microsoft Azure Media Services můžete od služby obdržet chybové kódy HTTP v závislosti na problémech, jako je například vyprší platnost ověřovacích tokenů k akcím, které nejsou podporovány v Media Services. Následuje seznam **kódů chyb HTTP** , které mohou být vráceny Media Services a možnými příčinami.  

## <a name="400-bad-request"></a>400 – Chybný požadavek
Požadavek obsahuje neplatné informace a byl odmítnut z některého z následujících důvodů:

* Je zadaná Nepodporovaná verze rozhraní API. Nejaktuálnější verzi najdete v tématu [instalace Media Services REST APIm vývoji](media-services-rest-how-to-use.md).
* Není zadaná verze rozhraní API Media Services. Informace o tom, jak zadat verzi rozhraní API, najdete v tématu [Media Services operations REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Pokud se k připojení Media Services používáte sady SDK .NET nebo Java, verze rozhraní API se zadává za vás pokaždé, když se pokusíte provést nějakou akci s Media Services.
  > 
  > 
* Byla zadána nedefinovaná vlastnost. Název vlastnosti je v chybové zprávě. Zadat lze pouze vlastnosti, které jsou členy dané entity. Seznam entit a jejich vlastností najdete v tématu [Azure Media Services REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference) .
* Byla zadána neplatná hodnota vlastnosti. Název vlastnosti je v chybové zprávě. Prohlédněte si předchozí odkaz na platné typy vlastností a jejich hodnoty.
* Hodnota vlastnosti chybí a je povinná.
* Část zadané adresy URL obsahuje chybnou hodnotu.
* Byl proveden pokus o aktualizaci vlastnosti WriteOnce.
* Došlo k pokusu o vytvoření úlohy, která má vstupní Asset s primárním AssetFile, který nebyl zadán, nebo jej nelze určit.
* Byl proveden pokus o aktualizaci lokátoru SAS. Lokátory SAS lze vytvořit nebo odstranit pouze. Lokátory streamování se dají aktualizovat. Další informace najdete v tématu [Lokátory](/rest/api/media/operations/locator).
* Byla odeslána Nepodporovaná operace nebo dotaz.

## <a name="401-unauthorized"></a>401 – Neautorizováno
Požadavek nebylo možné ověřit (před tím, než může být autorizován) z jednoho z následujících důvodů:

* Záhlaví ověřování chybí.
* Chybná hodnota hlavičky ověřování
  * Platnost tokenu vypršela. 
  * Token obsahuje neplatný podpis.

## <a name="403-forbidden"></a>403 – Zakázáno
Požadavek není povolen z některého z následujících důvodů:

* Účet Media Services nebyl nalezen nebo byl odstraněn.
* Účet Media Services je zakázán a typ požadavku není HTTP GET. Operace se službou vrátí taky odpověď 403.
* Ověřovací token neobsahuje informace o přihlašovacích údajích uživatele: Account/nebo SubscriptionId. Tyto informace najdete v Media Services rozšíření uživatelského rozhraní pro účet Media Services v Azure Portál pro správu.
* Prostředek není k dispozici.
  
  * Byl proveden pokus o použití MediaProcessor, který není k dispozici pro váš účet Media Services.
  * Byl proveden pokus o aktualizaci JobTemplate definovaného pomocí Media Services.
  * Došlo k pokusu o přepsání jiného lokátoru účtu Media Services.
  * Došlo k pokusu o přepsání nějakého ContentKeyu jiného účtu Media Services.
* Prostředek se nepovedlo vytvořit kvůli kvótě služby, která se dosáhla Media Services účtu. Další informace o kvótách služby najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Nenalezeno
Požadavek se u prostředku nepovoluje kvůli jednomu z následujících důvodů:

* Byl proveden pokus o aktualizaci entity, která neexistuje.
* Byl proveden pokus o odstranění neexistující entity.
* Byl proveden pokus o vytvoření entity, která odkazuje na neexistující entitu.
* Byl proveden pokus o získání neexistující entity.
* Byl proveden pokus o určení účtu úložiště, který není spojen s účtem Media Services.  

## <a name="409-conflict"></a>409 – Konflikt
Požadavek není povolen z některého z následujících důvodů:

* V rámci Assetu má zadaný název více než jeden AssetFile.
* V rámci Assetu byl proveden pokus o vytvoření druhého primárního AssetFile.
* Došlo k pokusu o vytvoření ContentKey se již použitým zadaným ID.
* Došlo k pokusu o vytvoření lokátoru se zadaným ID, které už bylo použito.
* Více než jeden IngestManifestFile má zadaný název v rámci IngestManifest.
* Došlo k pokusu o propojení druhého šifrování úložiště ContentKey s prostředkem šifrovaným úložištěm.
* Byl proveden pokus o propojení stejného ContentKey k Assetu.
* Došlo k pokusu o vytvoření lokátoru k Assetu, u kterého chybí kontejner úložiště nebo který už není přidružený k Assetu.
* Došlo k pokusu o vytvoření lokátoru k prostředku, který již má 5 lokátorů, které se používají. (Azure Storage vynutilo omezení pěti zásad sdíleného přístupu na jednom kontejneru úložiště.)
* Propojení účtu úložiště s IngestManifestAsset se neshoduje s účtem úložiště použitým nadřazeným IngestManifest.  

## <a name="500-internal-server-error"></a>500 – Vnitřní chyba serveru
Při zpracování požadavku Media Services dojde k nějaké chybě, která brání v pokračování zpracování. Může to být způsobené jedním z následujících důvodů:

* Vytvoření prostředku nebo úlohy se nepovede, protože informace o kvótě služby Media Services účtu jsou dočasně nedostupné.
* Vytváření Assetu nebo kontejneru úložiště objektů BLOB IngestManifest se nepovede, protože informace o účtu úložiště jsou dočasně nedostupné.
* Jiná Neočekávaná chyba.

## <a name="503-service-unavailable"></a>503 – Nedostupná služba
Server momentálně nemůže přijímat požadavky. Příčinou této chyby může být nadměrné množství požadavků na službu. Mechanismus omezování služby Media Services omezuje využití prostředků pro aplikace, které provádějí nadměrné množství požadavků na službu.

> [!NOTE]
> Podrobnější informace o důvodu, proč se zobrazuje chyba 503, najdete v chybové zprávě a řetězci kódu chyby. Tato chyba ne vždy znamená, že dochází k omezování.
> 
> 

Možné popisy stavu:

* "Server je zaneprázdněn. Předchozí spuštění tohoto typu žádosti trvalo déle než {0} sekund.
* "Server je zaneprázdněn. Více než {0} požadavků za sekundu lze omezit. "
* "Server je zaneprázdněn. Více než {0} žádostí během {1} několika sekund může být omezené. "

Pro zpracování této chyby doporučujeme použít exponenciální zpětnou logiku opakování. To znamená, že pomocí postupného čekání mezi opakovanými pokusy čekají na po sobě jdoucí chybové odpovědi.  Další informace naleznete v tématu [blok aplikace zpracování přechodných chyb](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> Pokud používáte [sadu Azure Media Services SDK pro .NET](https://github.com/Azure/azure-sdk-for-media-services/tree/master), je logika opakování pro chybu 503 implementovaná sadou SDK.  
> 
> 

## <a name="see-also"></a>Viz také
[Kódy chyb správy Media Services](/rest/api/media/)

## <a name="next-steps"></a>Další kroky
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
