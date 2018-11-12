---
title: Kódy chyb služby Azure Media Services | Dokumentace Microsoftu
description: Téma s přehledem kódy chyb služby Azure Media Services.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: d485195b868b6cbca587c736ac02663491742cdb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236870"
---
# <a name="azure-media-services-error-codes"></a>Kódy chyb služby Azure Media Services
Při použití Microsoft Azure Media Services, můžete obdržet chybových kódech HTTP ze služby v závislosti na problémy, jako je ověřování tokenů zanedlouho vyprší. na akce, které nejsou podporované ve službě Media Services. Tady je seznam **chybových kódech HTTP** , které mohou být vráceny Media Services a možné příčiny pro ně.  

## <a name="400-bad-request"></a>400 – Chybný požadavek
Požadavek obsahuje neplatné informace a je odmítnuto kvůli jednomu z následujících důvodů:

* Je zadána Nepodporovaná verze rozhraní API. Nejaktuálnější verzi, naleznete v tématu [instalace pro vývoj pro Media Services REST API](media-services-rest-how-to-use.md).
* Není určená verze rozhraní API služby Media Services. Informace o tom, jak určit verzi rozhraní API najdete v tématu [Reference k rozhraní API REST Media Services Operations](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Pokud používáte .NET nebo sady Java SDK pro připojení ke službě Media Services, verze rozhraní API je určena automaticky pokaždé, když akci a provést některé akce ve službě Media Services.
  > 
  > 
* Byla zadána nedefinovanou vlastnost. Název vlastnosti je v chybové zprávě. Je možné zadat jenom vlastnosti, které jsou členy dané entity. Zobrazit [Reference k rozhraní API REST Azure Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) seznam entit a jejich vlastnosti.
* Byla zadána neplatnou hodnotu vlastnosti. Název vlastnosti je v chybové zprávě. Podívejte se na předchozí odkaz pro platnou vlastnost typů a jejich hodnot.
* Hodnota vlastnosti nebyl nalezen a je povinný.
* Část adresy URL zadané obsahuje chybná hodnota.
* Došlo pokusu o umožňuje aktualizovat vlastnost WriteOnce.
* Došlo pokusu o vytvoření úlohy, která má vstupní Asset s primární AssetFile, který se nezadalo nebo nebylo možné určit.
* Došlo k pokusu o aktualizaci lokátoru SAS. Lokátory SAS můžou jenom vytvořit nebo odstranit. Lokátory streamování je možné aktualizovat. Další informace najdete v tématu [lokátory](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nepodporovaná operace nebo dotaz byla odeslána.

## <a name="401-unauthorized"></a>401 Neautorizováno
Požadavek nešlo ověřit (dříve, než je možné autorizovat) kvůli jednomu z následujících důvodů:

* Chybí hlavička ověřování.
* Hodnota hlavičky Chyba ověření.
  * Vypršela platnost tokenu. 
  * Token obsahuje neplatný podpis.

## <a name="403-forbidden"></a>403 Zakázáno
Žádost není povolena kvůli jednomu z následujících důvodů:

* Účet Media Services nebyl nalezen nebo se odstranil.
* Účet Media Services je zakázaný a není typ požadavku HTTP GET. Operace služby vrátí 403 odpověď.
* Ověřovací token neobsahuje přihlašovací údaje uživatele: název účtu a/nebo ID předplatného. Tyto informace najdete v rozšíření Media Services uživatelského rozhraní pro váš účet Media Services na portálu Azure Management Portal.
* Nelze získat přístup k prostředku.
  
  * Byl proveden pokus o použití MediaProcessor, který není k dispozici pro váš účet Media Services.
  * Došlo pokusu o aktualizaci JobTemplate definovány službou Media Services.
  * Došlo k pokusu o přepsání některých jiných účtu Media Services pro Lokátor.
  * K přepsání některých jiných účtu Media Services na ContentKey byl proveden pokus o.
* Prostředek nelze vytvořit z důvodu kvótu, bylo dosaženo pro účet Media Services. Další informace o kvótách služby najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nenalezeno
Žádost není povolená u prostředku kvůli jednomu z následujících důvodů:

* Byl proveden pokus o aktualizovat entitu, která neexistuje.
* Došlo k pokusu o odstranění entity, která neexistuje.
* Došlo pokusu o vytvoření entity, který odkazuje na objekt, který neexistuje.
* Došlo pokusu o NAČTENÍ entity, která neexistuje.
* Došlo pokusu o zadejte účet úložiště, který není přidružen k účtu Media Services.  

## <a name="409-conflict"></a>409 – konflikt
Žádost není povolena kvůli jednomu z následujících důvodů:

* Více než jeden AssetFile má zadaný název v rámci prostředku.
* Byl proveden pokus o vytvoření druhého primární AssetFile v rámci prostředku.
* Byl proveden pokus o vytvoření ContentKey se zadaným Id už používá.
* Došlo pokusu vytvořit lokátor se zadaným Id už používá.
* Více než jeden IngestManifestFile má zadaný název v rámci IngestManifest.
* Došlo pokusu o druhý šifrování úložiště ContentKey propojit prostředek šifrované úložiště.
* Došlo pokusu o stejné ContentKey propojit prostředek.
* Došlo pokusu vytvořit lokátor pro určitý prostředek, jehož kontejner úložiště chybí nebo je už přidružený Asset.
* Pokud chcete vytvořit lokátor pro určitý prostředek, který již má 5 lokátory používá byl proveden pokus o. (Úložiště azure vynucuje omezení na jeden kontejner úložiště pět zásady sdíleného přístupu.)
* Propojení účtu úložiště prostředku IngestManifestAsset není stejný jako účet úložiště používané IngestManifest nadřazenou položkou.  

## <a name="500-internal-server-error"></a>500 – Interní chyba serveru
Během zpracování požadavku, Media Services nalezne nějaké chybě, která zabrání v pokračování zpracování. Může to být způsobené jedním z následujících důvodů:

* Vytváření prostředku nebo úloha selže, protože informace o kvótách služby účtu Media Services je dočasně nedostupná.
* Vytváří se prostředek nebo IngestManifest kontejneru úložiště objektů blob se nezdaří, protože informace o účtu úložiště na účet je dočasně nedostupná.
* Další neočekávané chyby.

## <a name="503-service-unavailable"></a>503 – Nedostupná služba
Server je nyní nelze přijmout požadavky. Tuto chybu může způsobovat nadměrné požadavky na službu. Mechanismus omezení služby Media Services omezuje využití prostředků pro aplikace, které usnadňují nadměrnou ke službě.

> [!NOTE]
> Zkontrolujte chybovou zprávu a řetězec kódu chyby, chcete-li získat podrobnější informace o důvodu, že jste získali došlo k chybě 503. Tato chyba nemusí to znamenat omezení šířky pásma.
> 
> 

Je to možné stav popisy jsou:

* "Server je zaneprázdněn. Trval předchozí běhy tohoto typu žádosti více než {0} sekund. "
* "Server je zaneprázdněn. Více než {0} můžu omezit počet požadavků za sekundu. "
* "Server je zaneprázdněn. Více než {0} požadavky v rámci {1} můžu omezit sekund. "

Pro zpracování této chybě, doporučujeme použít logiku opakování exponenciální regresní. To znamená, pomocí postupně déle čeká mezi jednotlivými pokusy o po sobě jdoucích chybové odpovědi.  Další informace najdete v tématu [přechodné Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Pokud používáte [Azure Media Services SDK pro .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logika opakovaných pokusů pro došlo k chybě 503 implementován sada SDK.  
> 
> 

## <a name="see-also"></a>Viz také
[Kódy chyb pro správu služby Media Services](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

