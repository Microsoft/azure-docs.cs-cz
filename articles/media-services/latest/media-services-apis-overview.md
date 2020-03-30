---
title: Vývoj s v3 API
titleSuffix: Azure Media Services
description: Informace o pravidlech, která se vztahují na entity a api při vývoji s Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472080"
---
# <a name="develop-with-media-services-v3-apis"></a>Vývoj pomocí mediálních služeb v3 API

Jako vývojář můžete používat rozhraní API [REST](https://docs.microsoft.com/rest/api/media/) služby Media Services nebo klientské knihovny, které umožňují interakci s rozhraním REST API a snadno vytvářet, spravovat a udržovat vlastní pracovní postupy médií. [Rozhraní API Media Services v3](https://aka.ms/ams-v3-rest-sdk) je založeno na specifikaci OpenAPI (dříve známé jako Swagger).

Tento článek popisuje pravidla, která platí pro entity a api při vývoji s Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Přístup k rozhraní API mediálních služeb Azure

Chcete-li získat oprávnění k přístupu k prostředkům mediálních služeb a rozhraní API mediálních služeb, musíte být nejprve ověřeni. Mediální služby podporují ověřování [na základě Azure Active Directory (Azure AD).](../../active-directory/fundamentals/active-directory-whatis.md) Dvě běžné možnosti ověřování jsou:
 
* **Ověřování instančního objektu**: Používá se k ověření služby (například: webové aplikace, aplikace funkcí, aplikace logiky, rozhraní API a mikroslužeb). Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby daemon, služby střední vrstvy nebo naplánované úlohy. Například pro webové aplikace by měla být vždy střední vrstvy, která se připojuje k Media Services s instanční.
* **Ověřování uživatelů**: Používá se k ověření osoby, která používá aplikaci k interakci s prostředky Mediálních služeb. Interaktivní aplikace by měla nejprve vyzvat uživatele k zadání pověření uživatele. Příkladem je aplikace konzoly pro správu používaná oprávněnými uživateli ke sledování úloh kódování nebo živého streamování.

Rozhraní API mediálních služeb vyžaduje, aby uživatel nebo aplikace, která provádí požadavky rozhraní REST API, měli přístup k prostředku účtu Mediální služby a používali roli **přispěvatele** nebo **vlastníka.** Rozhraní API lze přistupovat s rolí **Čtečka,** ale budou k dispozici pouze operace **Get** nebo **List.**Další informace naleznete [v tématu Řízení přístupu na základě rolí pro účty Mediálních služeb](rbac-overview.md).

Místo vytváření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro přístup k rozhraní API mediálních služeb prostřednictvím Správce prostředků Azure. Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [Co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Instanční objekt Azure AD

Pokud vytváříte aplikaci Azure AD a instanční objekt služby, musí být aplikace ve vlastním tenantovi. Po vytvoření aplikace udělit **aplikaci přispěvatel nebo** **vlastník** role přístup k účtu Mediální služby.

Pokud si nejste jistí, jestli máte oprávnění k vytvoření aplikace Azure AD, přečtěte si informace [o požadovaných oprávněních](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na následujícím obrázku představují čísla tok požadavků v chronologickém pořadí:

![Ověřování aplikací střední vrstvy pomocí AAD z webového rozhraní API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikace střední vrstvy požaduje přístupový token Azure AD, který má následující parametry:  

   * Koncový bod klienta Azure AD.
   * Identifikátor URI prostředku mediálních služeb.
   * Identifikátor URI prostředků pro službu REST Media Services.
   * Hodnoty aplikace Azure AD: ID klienta a tajný klíč klienta.

   Pokud chcete získat všechny potřebné hodnoty, [přečtěte si přístup k rozhraní API Azure Media Services pomocí rozhraní příkazového příkazu k síti Azure](access-api-cli-how-to.md).

2. Přístupový token Azure AD se odesílá do střední vrstvy.
4. Střední vrstva odesílá požadavek do rozhraní AZURE Media REST API s tokenem Azure AD.
5. Střední vrstva získá zpět data z Media Services.

### <a name="samples"></a>ukázky

Podívejte se na následující ukázky, které ukazují, jak se připojit k instančnímu objektu služby Azure AD:

* [Spojte se s REST](media-rest-apis-with-postman.md)  
* [Připojení s využitím Javy](configure-connect-java-howto.md)
* [Připojení s využitím .NET](configure-connect-dotnet-howto.md)
* [Připojení s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojení s využitím Pythonu](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID).

Názvy prostředků mediálních služeb nemohou obsahovat: <,, ">", "&", "&", "&#92;", "", "/,,*, '+,, '.', znak jediné nabídky nebo jakékoli řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků.

Další informace o pojmenování Služby Azure Resource Manager najdete v [tématu Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [Konvence pojmenování](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Názvy souborů nebo objektů BLOB v rámci datového zdroje

Názvy souborů/objektů BLOB v rámci datového zdroje musí dodržovat [požadavky na název objektu blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) i [požadavky na název systému souborů](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem pro tyto požadavky je, že soubory mohou být zkopírovány z úložiště objektů blob na místní disk NTFS pro zpracování.

## <a name="long-running-operations"></a>Dlouhotrvající provoz

Operace označené `x-ms-long-running-operation` v azure media services [naparování soubory](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) jsou dlouho běžící operace. 

Podrobnosti o tom, jak sledovat asynchronní operace Azure, naleznete [v tématu Asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Služba Media Services má následující dlouhodobé operace:

* [Vytvořit živé události](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aktualizace živých událostí](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Odstranit živou událost](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Spustit živou událost](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Zastavit LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Pomocí `removeOutputsOnStop` parametru odstraňte všechny přidružené živé výstupy při zastavení události.  
* [Obnovit liveevent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Vytvořit liveoutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Odstranit živý výstup](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Vytvořit datový proudEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aktualizovat datový proudEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Odstranit streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Spustit StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Zastavit streamingendpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Měřítko koncového bodu streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Při úspěšném odeslání dlouhé operace obdržíte "202 Accepted" a musí být dotazováno na dokončení operace pomocí Vráceného ID operace.

[Track asynchronní Azure operations](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) článek vysvětluje do hloubky, jak sledovat stav asynchronní operace Azure prostřednictvím hodnot vrácených v odpovědi.

Pro danou živou událost nebo některý z přidružených živých výstupů je podporována pouze jedna dlouhotrvající operace. Po spuštění musí být před spuštěním následné dlouhotrvající operace na stejné službě LiveEvent nebo přidružených živých výstupech dokončena dlouho běžící operace. Pro živé události s více živými výstupy musíte počkat na dokončení dlouhotrvající operace na jednom živém výstupu před spuštěním dlouhotrvající operace na jiném živém výstupu. 

## <a name="sdks"></a>Sady SDK

> [!NOTE]
> Sady Azure Media Services v3 SDK nejsou zaručeně bezpečné pro přístup z více vláken. Při vývoji aplikace s více vlákny byste měli přidat vlastní logiku synchronizace vláken k ochraně klienta nebo použít nový objekt AzureMediaServicesClient na vlákno. Měli byste být také opatrní více vláken problémy zavedené volitelné objekty poskytované kódem klientovi (jako httpclient instance v .NET).

|Sada SDK|Odkaz|
|---|---|
|[Sada SDK rozhraní .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referenční informace k .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referenční informace k Javě](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referenční informace k Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referenční informace k jazyku Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Viz také

- [Sada EventGrid .NET SDK, která zahrnuje události služby Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí mediálních služeb](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj dostupný zákazníkům s Windows, kteří se chtějí dozvědět o mediálních službách. AMSE je Winforms / C # aplikace, která se nahrát, stáhnout, kódovat, stream Ovat VOD a živý obsah s Media Services. Nástroj AMSE je určen pro klienty, kteří chtějí testovat mediální služby bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet s Media Services.

AMSE je open source projekt, podpora je poskytována komunitou https://github.com/Azure/Azure-Media-Services-Explorer/issues)(problémy mohou být hlášeny . Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace naleznete v [nejčastějších dotazech k Kodexu chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte opencode@microsoft.com na jakékoli další dotazy nebo připomínky.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování entit Mediálních služeb

Viz [Filtrování, řazení, stránkování entit Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ptejte se, podávejte zpětnou vazbu, získejte aktualizace

Podívejte se na článek [komunity Mediálních služeb Azure](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, poskytovat zpětnou vazbu a získat aktualizace o mediálních službách.

## <a name="see-also"></a>Viz také

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Další kroky

* [Připojení k mediálním službám pomocí javy](configure-connect-java-howto.md)
* [Připojení k mediálním službám pomocí rozhraní .NET](configure-connect-dotnet-howto.md)
* [Připojení ke službě Media Services pomocí souboru Node.js](configure-connect-nodejs-howto.md)
* [Připojení k mediálním službám pomocí Pythonu](configure-connect-python-howto.md)
