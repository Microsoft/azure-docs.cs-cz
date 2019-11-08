---
title: Vývoj s použitím rozhraní V3 API – Azure | Microsoft Docs
description: Tento článek popisuje pravidla, která se vztahují na entity a rozhraní API při vývoji s Media Services V3.
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
ms.openlocfilehash: 0c263e1353a07ca388ea9a7fb48ebcf99be07fc1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820639"
---
# <a name="developing-with-media-services-v3-apis"></a>Vývoj s využitím rozhraní API Media Services V3

Jako vývojář můžete použít Media Services [REST API](https://aka.ms/ams-v3-rest-ref) nebo klientské knihovny, které vám umožní pracovat s REST API, abyste mohli snadno vytvářet, spravovat a spravovat vlastní pracovní postupy pro média. Rozhraní [Media Services V3](https://aka.ms/ams-v3-rest-sdk) API je založené na specifikaci openapi (dříve označované jako Swagger).

Tento článek popisuje pravidla, která se vztahují na entity a rozhraní API při vývoji s Media Services V3.

## <a name="accessing-the-azure-media-services-api"></a>Přístup k rozhraní Azure Media Services API

Aby bylo možné získat přístup k Media Services prostředkům a rozhraní Media Services API, musíte nejdřív ověřit. Media Services podporuje ověřování [založené na Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) . Mezi dvě běžné možnosti ověřování patří:
 
* **Ověřování instančního objektu** – používá se k ověření služby (například webové aplikace, aplikace Function App, Logic Apps, API a mikroslužby). Aplikace, které běžně používají tuto metodu ověřování, jsou aplikace, které spouštějí služby démon, služby střední vrstvy nebo naplánované úlohy. Například pro webové aplikace by měla být vždy střední vrstva, která se připojuje k Media Services s instančním objektem.
* **Ověřování uživatele** – používá se k ověření osoby, která aplikaci používá k interakci s Media Servicesmi prostředky. Interaktivní aplikace by nejdřív měla uživatele vyzvat k zadání přihlašovacích údajů uživatele. Příkladem je aplikace konzoly pro správu používaná autorizovanými uživateli k monitorování úloh kódování nebo živého streamování.

Rozhraní Media Services API vyžaduje, aby uživatel nebo aplikace, které mají žádosti REST API, měly přístup k prostředku Media Services účtu a používají roli **Přispěvatel** nebo **vlastník** . K rozhraní API se dá získat přístup **, ale** budou k dispozici jenom operace **Get** nebo **list** . Další informace najdete v tématu [řízení přístupu na základě role pro účty Media Services](rbac-overview.md).

Místo Vytvoření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro přístup k rozhraní Media Services API prostřednictvím Azure Resource Manager. Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [co jsou spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Instanční objekt služby Azure AD 

Pokud vytváříte aplikaci a instanční objekt služby Azure AD, musí být aplikace ve vlastním tenantovi. Po vytvoření aplikace udělte **přispěvateli** aplikace nebo roli **vlastníka** přístup k účtu Media Services. 

Pokud si nejste jistí, jestli máte oprávnění k vytvoření aplikace služby Azure AD, přečtěte si téma [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na následujícím obrázku čísla reprezentují tok požadavků v chronologickém pořadí:

![Aplikace střední vrstvy](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikace střední vrstvy požaduje přístupový token Azure AD s následujícími parametry:  

   * Koncový bod tenanta Azure AD.
   * Media Services identifikátor URI prostředku.
   * Identifikátor URI prostředku pro Media Services REST
   * Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta.
   
   Pokud chcete získat všechny potřebné hodnoty, přečtěte si téma [přístup k rozhraní API Azure Media Services pomocí Azure CLI](access-api-cli-how-to.md) .

2. Přístupový token Azure AD se pošle do střední úrovně.
4. Střední vrstva odesílá požadavek do Azure Media REST API s tokenem Azure AD.
5. Střední vrstva vrátí data z Media Services.

### <a name="samples"></a>Ukázky

Podívejte se na následující ukázky, které ukazují, jak se připojit pomocí instančního objektu služby Azure AD:

* [Připojit se pomocí REST](media-rest-apis-with-postman.md)  
* [Připojení s využitím Javy](configure-connect-java-howto.md)
* [Připojení s využitím .NET](configure-connect-dotnet-howto.md)
* [Připojení s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojení s využitím Pythonu](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o vytváření názvů Azure Resource Manageru najdete v tématech věnovaných [požadavkům na vytváření názvů](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásadám vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Názvy souborů/objektů BLOB v rámci assetu

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) a [požadavky na název systému souborů NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="long-running-operations"></a>Dlouhodobě běžící operace

Operace označené `x-ms-long-running-operation` v Azure Media Services [soubory Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) jsou dlouhodobě běžící operace. 

Podrobnosti o tom, jak sledovat asynchronní operace Azure, najdete v tématu [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services má následující dlouhodobě běžící operace:

* [Vytváření živých událostí](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aktualizovat živé události](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Odstranit živou událost](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Spustit živou událost](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Zastavit Livestream](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Při zastavování události použijte parametr `removeOutputsOnStop` k odstranění všech přidružených výstupů živého vysílání.  
* [Resetovat Livestream](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Vytvořit LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Odstranit LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Vytvořit StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aktualizovat StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Odstranit StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Spustit StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Zastavit StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Škálování StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Po úspěšném odeslání dlouhé operace obdržíte 202 přijato a musíte se dotázat na dokončení operace pomocí vráceného ID operace.

Článek [sledování asynchronních operací Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) podrobněji vysvětluje, jak sledovat stav asynchronních operací Azure prostřednictvím hodnot vrácených v odpovědi.

Pro danou živou událost nebo jakýkoli z přidružených výstupů živého vysílání je podporována pouze jedna dlouhodobě běžící operace. Po spuštění se musí dlouho běžící operace dokončit před spuštěním následné dlouhotrvající operace na stejném Livestream nebo jakýchkoli přidružených živých výstupech. U živých událostí s více živými výstupy musíte očekávat dokončení dlouhotrvající operace na jednom aktivním výstupu před aktivací dlouhotrvající operace na jiném živém výstupu. 

## <a name="sdks"></a>Sady SDK

> [!NOTE]
> Sady SDK Azure Media Services V3 nejsou zaručeny jako bezpečné pro přístup z více vláken. Při vývoji vícevláknové aplikace byste měli přidat vlastní logiku synchronizace vláken pro ochranu klienta nebo použití nového objektu AzureMediaServicesClient na vlákno. Měli byste také dbát na problémy s více vlákny, které přináší volitelné objekty poskytované vaším kódem, klientovi (například instance HttpClient v rozhraní .NET).

|Sada SDK|Referenční informace|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Referenční informace k .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referenční informace k Javě](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referenční informace k Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referenční informace k jazyku Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Viz také

- [EventGrid .NET SDK zahrnující události Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj dostupný pro zákazníky s Windows, kteří se chtějí dozvědět o Media Services. AMSE je WinForms neboC# aplikace, které odesílají, stahují, kódují, streamování vod a živý obsah pomocí Media Services. Nástroj AMSE je určen pro klienty, kteří chtějí testovat Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet pomocí Media Services.

AMSE je otevřený zdrojový projekt. Podpora je poskytována komunitou (problémy mohou být hlášeny https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v tématu [Nejčastější dotazy týkající se kódu, časté](https://opensource.microsoft.com/codeofconduct/faq/) otázky nebo kontaktování opencode@microsoft.com s dalšími dotazy nebo komentáři.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování Media Services entit

Viz [filtrování, řazení, stránkování Azure Media Services entit](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Další kroky

* [Připojení k Media Services pomocí Java](configure-connect-java-howto.md)
* [Připojení k Media Services pomocí .NET](configure-connect-dotnet-howto.md)
* [Připojení k Media Services pomocí Node. js](configure-connect-nodejs-howto.md)
* [Připojení k Media Services pomocí Pythonu](configure-connect-python-howto.md)
