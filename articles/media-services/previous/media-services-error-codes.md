---
title: Kódy chyb Azure Media Services | Microsoft Docs
description: Téma nabízí přehled kódů chyb Azure Media Services.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 8a374393a6a5b3d563a441654b7b5df8a510f304
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788764"
---
# <a name="azure-media-services-error-codes"></a>Kódy chyb Azure Media Services
Při použití Microsoft Azure Media Services, může se zobrazit kódy chyb HTTP ze služby v závislosti na problémy, jako je například ověřování tokenů vypršení platnosti na akce, které nejsou podporovány ve službě Media Services. Následuje seznam **kódy chyb HTTP** , mohou být vráceny Media Services a možné příčiny pro ně.  

## <a name="400-bad-request"></a>400 – Chybný požadavek
Žádost obsahuje neplatné informace a se odmítne kvůli jednomu z následujících důvodů:

* Je zadána Nepodporovaná verze rozhraní API. Nejnovější verzi, naleznete v části [instalační program pro Media Services REST API vývoj](media-services-rest-how-to-use.md).
* Není určená verze rozhraní API služby Media Services. Informace o tom, jak určit verze rozhraní API najdete v tématu [média referenční dokumentace rozhraní API REST služby Operations](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Pokud používáte k připojení ke službě Media Services .NET nebo sady Java SDK, verze rozhraní API je určena pro vás vždy, když akci a provedení několika akcí proti Media Services.
  > 
  > 
* Nedefinovaná vlastnost nebyla zadána. Název vlastnosti je v chybové zprávě. Lze zadat pouze vlastnosti, které jsou členy dané entity. V tématu [Azure Media Services REST API – referenční informace](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) seznam entity a jejich vlastnosti.
* Byla zadána neplatnou hodnotu vlastnosti. Název vlastnosti je v chybové zprávě. Najdete odkaz na předchozí platnou vlastnost typy a jejich hodnoty.
* Hodnota vlastnosti je chybějící a musí se.
* Součást zadaná adresa URL obsahuje neplatná hodnota.
* Došlo pokusu o aktualizujte vlastnost WriteOnce.
* Došlo pokusu vytvořit úlohu, která má vstupní Asset primární AssetFile, který nebyl zadán nebo nebylo možné určit.
* Došlo pokusu o aktualizaci lokátoru SAS. Lokátory SAS můžou jenom vytvořit nebo odstranit. Lokátory streamování lze aktualizovat. Další informace najdete v tématu [lokátory](https://docs.microsoft.com/rest/api/media/operations/locator).
* Nepodporovanou operaci nebo dotaz byla odeslána.

## <a name="401-unauthorized"></a>401 unauthorized
Žádost nešlo ověřit (dříve, než lze udělit oprávnění) kvůli jednomu z následujících důvodů:

* Chybí záhlaví ověřování.
* Hodnota hlavičky chybný ověřování.
  * Platnost tokenu vypršela. 
  * Token obsahuje neplatný podpis.

## <a name="403-forbidden"></a>403 Zakázáno
Požadavek není povoleno z důvodu jednu z následujících důvodů:

* Účet Media Services nebyl nalezen nebo je odstraněný.
* Účet Media Services je zakázán a není typ požadavku HTTP GET. Operace služby vrátí 403 odpověď.
* Ověřovací token neobsahuje přihlašovací údaje uživatele: název účtu nebo ID předplatného. Tyto informace najdete v rozšíření Media Services uživatelského rozhraní pro váš účet Media Services v Azure Management Portal.
* Nelze získat přístup k prostředku.
  
  * Došlo pokusu o použití MediaProcessor, který není k dispozici pro váš účet Media Services.
  * Byl proveden pokus o aktualizace JobTemplate definované Media Services.
  * Byl proveden pokus o přepsat některé jiné účtu Media Services je lokátoru.
  * Byl proveden pokus o přepsat ContentKey některé jiné Media Services účtu.
* Prostředek nelze vytvořit z důvodu kvótu služby, bylo dosaženo pro účet Media Services. Další informace o kvót služby najdete v tématu [kvóty a omezení](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Nenalezeno
Žádost není povolená na prostředku kvůli jednomu z následujících důvodů:

* Došlo pokusu o aktualizovat entitu, která neexistuje.
* Došlo pokusu o odstranění entity, která neexistuje.
* Došlo pokusu o vytvoření entity, který odkazuje na entity, která neexistuje.
* Došlo k pokusu o NAČTENÍ entity, která neexistuje.
* Došlo pokusu o zadejte účet úložiště, který není přidružen k účtu Media Services.  

## <a name="409-conflict"></a>409 – konflikt
Požadavek není povoleno z důvodu jednu z následujících důvodů:

* Více než jeden AssetFile má zadaný název v rámci prostředku.
* Byl proveden pokus o vytvoření druhého primární AssetFile v rámci prostředku.
* Došlo k pokusu o vytvoření ContentKey se zadaným Id už používá.
* Došlo pokusu vytvořit lokátor se zadaným Id už používá.
* Více než jeden IngestManifestFile má zadaný název v rámci IngestManifest.
* Došlo pokusu o druhý šifrování úložiště ContentKey propojit Asset šifrovat úložiště.
* Byl proveden pokus o propojení stejné ContentKey pro daný prostředek.
* Došlo pokusu vytvořit lokátor pro prostředek, jehož kontejner úložiště chybí nebo je už přidružený Asset.
* Došlo pokusu vytvořit lokátor pro prostředek, který již má 5 lokátory používá. (Úložiště azure vynucuje omezení pět zásady sdíleného přístupu na jeden kontejner úložiště.)
* Propojování účtu úložiště prostředek IngestManifestAsset není stejný jako účet úložiště využívá nadřízený objekt IngestManifest.  

## <a name="500-internal-server-error"></a>500 – Interní chyba serveru
Během zpracování požadavku, zaznamená Media Services nějaká chyba, která zabraňuje zpracování pokračovat. Může to být způsobené jedním z následujících důvodů:

* Vytvoření úlohy nebo Asset nezdaří, protože informace o kvótě účtu Media Services služba je dočasně nedostupná.
* Vytvoření prostředku nebo IngestManifest kontejner úložiště objektů blob se nezdaří, protože informace o účtu úložiště na účet je dočasně nedostupná.
* Další neočekávané chyby.

## <a name="503-service-unavailable"></a>503 – Nedostupná služba
Server je nyní nelze přijmout požadavky. Tato chyba může být způsobeno nadměrné požadavky na službu. Služba Media Services omezení mechanismus omezuje využití prostředků pro aplikace, které provést nadměrné požadavek na službu.

> [!NOTE]
> Zkontrolujte chybovou zprávou a řetězec kódu chyby. Chcete-li získat podrobnější informace o příčině, že jste získali 503 chyby. Tato chyba vždy neznamená omezení.
> 
> 

Možné stav popisy jsou:

* "Server je zaneprázdněn. Předchozí spustí tento typ požadavku trvalo víc než {0} sekund. "
* "Server je zaneprázdněn. Více než {0} požadavků za sekundu může omezeny. "
* "Server je zaneprázdněn. Více než {0} požadavky v rámci {1} sekund můžete omezeny. "

Pro zpracování této chybě, doporučujeme používat logiku exponenciální opakování back vypnout. To znamená, pomocí progresivně delší čeká mezi jednotlivými pokusy o po sobě jdoucích chybové odpovědi.  Další informace najdete v tématu [přechodné chyby zpracování bloku aplikace](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Pokud používáte [Azure Media Services SDK pro .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logiku opakovaných pokusů pro chyby 503 nebyla implementovaná sadou SDK.  
> 
> 

## <a name="see-also"></a>Viz také
[Kódy chyb správy služby Media Services](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Další postup
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

