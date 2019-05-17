---
title: Vývoj s využitím rozhraní API v3 – Azure | Dokumentace Microsoftu
description: Tento článek popisuje pravidla, které se vztahují k entitám a rozhraním API při vývoji pomocí Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3ce20b56fc2cbebbed4b525eeccc2c12d14cccc3
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556231"
---
# <a name="developing-with-media-services-v3-apis"></a>Vývoj s využitím Media Services v3 rozhraní API

Jako vývojář, můžete použít Media Services [rozhraní REST API](https://aka.ms/ams-v3-rest-ref) nebo klientských knihoven, které umožňují pracovat s rozhraním REST API snadno vytvářet, spravovat a udržovat vlastní multimediální pracovní postupy. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) rozhraní API je založena na specifikaci OpenAPI (dříve označované jako Swagger).

Tento článek popisuje pravidla, které se vztahují k entitám a rozhraním API při vývoji pomocí Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Přístup k Azure Media Services rozhraní API

K autorizaci pro přístup k prostředkům služby Media Services a rozhraní API služby Media Services, musíte nejprve být ověřeni. Služba Media Services podporuje [Azure Active Directory (Azure AD) – na základě](../../active-directory/fundamentals/active-directory-whatis.md) ověřování. Jsou dvě běžné možnosti ověřování:
 
* **Ověřování instančních objektů** – slouží k ověření služby (například: webové aplikace, aplikace function App, logic apps, rozhraní API a mikroslužeb). Aplikace, které se běžně používají tuto metodu ověřování jsou aplikace, na kterých běží služby démonů, střední vrstvy služby nebo podle plánu. Pro webové aplikace existuje by měl vždy být například střední vrstvy, která se připojuje ke službě Media Services pomocí instančního objektu.
* **Ověřování uživatelů** – slouží k ověření osoba, která používá aplikace k interakci s prostředky služby Media Services. Interaktivní aplikace by měly nejprve vyzve uživatele k přihlašovacím údajům uživatele. Příkladem je aplikace konzoly správy Autorizovaní uživatelé používají ke sledování úloh kódování nebo živého streamování.

Rozhraní API služby Media Services vyžaduje, že požádá uživatele nebo aplikace, což rozhraní REST API mít přístup k prostředku účtu Media Services a používat **Přispěvatel** nebo **vlastníka** role. Rozhraní API můžete přistupovat pomocí **čtečky** role, ale pouze **získat** nebo **seznamu**   operace bude k dispozici. Další informace najdete v tématu [řízení přístupu na základě rolí pro účty Media Services](rbac-overview.md).

Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro přístup k rozhraní API služby Media Services prostřednictvím Azure Resource Manageru. Další informace o spravovaných identit pro prostředky Azure, najdete v článku [co je spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Instanční objekt Azure AD 

Pokud vytváříte aplikace Azure AD a služby instančního objektu, aplikace musí být ve vlastním tenanta. Po vytvoření aplikace, dejte aplikaci **Přispěvatel** nebo **vlastníka** role přístup k účtu Media Services. 

Pokud si nejste jisti, zda máte oprávnění k vytvoření aplikace Azure AD, najdete v článku [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Na následujícím obrázku představuje čísla tok žádostí v chronologickém pořadí:

![Aplikace střední vrstvy](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Střední vrstvy aplikace požaduje přístupový token Azure AD, který má následující parametry:  

   * Koncový bod pro tenanta Azure AD.
   * Identifikátor URI prostředku služby Media Services.
   * Identifikátor URI pro REST Media Services.
   * Hodnoty aplikace Azure AD: ID klienta a tajný kód klienta.
   
   Pokud chcete získat všechny potřebné hodnoty, najdete v článku [rozhraní API k přístupu k Azure Media Services pomocí rozhraní příkazového řádku Azure](access-api-cli-how-to.md)

2. Přístupový token Azure AD se odešle do střední vrstvy.
4. Střední vrstva odešle požadavek REST API služby Azure Media s tokenem Azure AD.
5. Střední vrstva získá zpět data ze služby Media Services.

### <a name="samples"></a>Ukázky kódu

Viz následující ukázky, které ukazují, jak se připojit pomocí instančního objektu Azure AD:

* [Spojte se s REST](media-rest-apis-with-postman.md)  
* [Připojení s využitím Javy](configure-connect-java-howto.md)
* [Připojení s využitím .NET](configure-connect-dotnet-howto.md)
* [Připojení s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojení s využitím Pythonu](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID). 

Názvy prostředků služby Media Services nemůže obsahovat znaky <, >, %, &, :, &#92;, ?, /, *, +, ., jednoduché uvozovky ani žádné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků. 

Další informace o zadávání názvů Azure Resource Manageru najdete v tématu: [Požadavky na pojmenování](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) a [zásady vytváření názvů](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Dlouho běžící operace

Operace označené `x-ms-long-running-operation` ve službě Azure Media Services [swagger soubory](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) se dlouho běžící operace. 

Podrobnosti o tom, jak sledovat asynchronních operací v Azure najdete v tématu [asynchronních operací v](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Služba Media Services má následující dlouho běžící operace:

* Vytvoření Livestream
* Aktualizace Livestream
* Odstranit Livestream
* Spustit Livestream
* Zastavit Livestream
* Resetovat Livestream
* Vytvoření LiveOutput
* Odstranit LiveOutput
* Vytvoření StreamingEndpoint
* Aktualizace StreamingEndpoint
* Odstranit StreamingEndpoint
* Spustit StreamingEndpoint
* Zastavit StreamingEndpoint
* Škálování StreamingEndpoint


## <a name="sdks"></a>Sady SDK

> [!NOTE]
> Sady SDK služby Azure Media Services v3 nemusí být bezpečné pro vlákna. Při vývoji aplikace Vícevláknová, měli byste přidat vlastní logiku synchronizace vlákno při ochraně klienta nebo použít nový objekt AzureMediaServicesClient na vlákno. Také byste měli být opatrní z více vláken problémy spojené s uvedením volitelné objekty poskytované kódu do klienta (například HttpClient instance v rozhraní .NET).

|SDK|Referenční informace|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Referenční informace k .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referenční informace k Javě](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referenční informace k Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referenční informace k jazyku Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Další informace najdete v tématech

- [EventGrid .NET SDK, která zahrnuje události mediálních služeb](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj Windows zákazníkům, kteří chtějí informace o Media Services k dispozici. AMSE je jazyce Winforms /C# aplikaci, která nahrání, stažení, kódování, streamování videa na Vyžádání a živého obsahu pomocí služby Media Services. Nástroj AMSE je pro klienty, kteří chtějí testování Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet s využitím Media Services.

AMSE je Opensourcový projekt, je podporované komunitou (jsou hlášeny problémy https://github.com/Azure/Azure-Media-Services-Explorer/issues). Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v článku [kódu k pravidlům chování](https://opensource.microsoft.com/codeofconduct/faq/) nebo se obraťte na opencode@microsoft.com s případnými dotazy nebo připomínky.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování entit Media Services

Zobrazit [filtrování, řazení, stránkování entit Azure Media Services](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Klást otázky, váš názor, získávat aktualizace

Podívejte se [komunita Azure Media Services](media-services-community.md) článek a zobrazit různé způsoby můžete klást otázky, poskytnout zpětnou vazbu a aktualizace o Media Services.

## <a name="next-steps"></a>Další postup

* [Připojit k Media Services s Javou](configure-connect-java-howto.md)
* [Připojit k Media Services s .NET](configure-connect-dotnet-howto.md)
* [Připojit k Media Services s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojit k Media Services s využitím Pythonu](configure-connect-python-howto.md)
