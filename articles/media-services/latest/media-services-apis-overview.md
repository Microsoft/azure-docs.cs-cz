---
title: Vývoj s využitím rozhraní API V3
titleSuffix: Azure Media Services
description: Přečtěte si o pravidlech, která se vztahují na entity a rozhraní API při vývoji s Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 4d1c68ba1e0b7db8afefa64abefc671d51e78bd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569517"
---
# <a name="develop-with-media-services-v3-apis"></a>Vývoj pomocí rozhraní API služby Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Jako vývojář můžete použít Media Services [REST API](/rest/api/media/) nebo klientské knihovny, které vám umožní pracovat s REST API, abyste mohli snadno vytvářet, spravovat a spravovat vlastní pracovní postupy pro média. Rozhraní [Media Services V3](https://aka.ms/ams-v3-rest-sdk) API je založené na specifikaci openapi (dříve označované jako Swagger).

Tento článek popisuje pravidla, která se vztahují na entity a rozhraní API při vývoji pomocí Media Services V3.

## <a name="accessing-the-azure-media-services-api"></a>Přístup k rozhraní API služby Azure Media Services

Abyste mohli získat autorizaci pro přístup k prostředkům a rozhraní API služby Media Services, musíte se nejprve ověřit. Služba Media Services podporuje ověřování [na základě Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). K dispozici jsou dvě běžné možnosti ověřování:
 
* **Ověřování instančních objektů:** Slouží k ověřování služeb (například webových aplikací, aplikací funkcí, aplikací logiky, rozhraní API nebo mikroslužeb). Mezi aplikace, které běžně využívají tuto metodu ověřování, patří aplikace spouštějící služby démonů, služby střední úrovně nebo naplánované úlohy. Například pro webové aplikace by měla být vždy střední vrstva, která se připojuje k Media Services s instančním objektem.
* **Ověřování uživatelů:** Slouží k ověřování lidí, kteří pomocí aplikace pracují s prostředky služby Media Services. Interaktivní aplikace by nejprve měla uživatele vyzvat k zadání přihlašovacích údajů. Příkladem je aplikace konzoly pro správu, kterou autorizovaní uživatelé používají k monitorování úloh kódování nebo živého streamování.

Rozhraní API služby Media Services vyžaduje, aby uživatel nebo aplikace provádějící požadavky rozhraní REST API měli přístup k prostředku účtu služby Media Services a používali roli **Přispěvatel** nebo **Vlastník**. K rozhraní API je možné získat přístup také s rolí **Čtenář**, ale v takovém případě budou k dispozici pouze operace **Get** a **List**.Další informace najdete v tématu [Řízení přístupu na základě role pro účty služby Media Services](rbac-overview.md).

Místo vytváření instančního objektu zvažte přístup k rozhraní API služby Media Services prostřednictvím Azure Resource Manageru s využitím spravovaných identit pro prostředky Azure. Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [Co jsou spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Instanční objekt služby Azure AD

Aplikace a instanční objekt služby Azure AD by měly být ve stejném tenantovi. Po vytvoření aplikace udělte **přispěvateli** aplikace nebo roli **vlastníka** přístup k účtu Media Services.

Pokud si nejste jistí, jestli máte oprávnění k vytvoření aplikace služby Azure AD, přečtěte si téma [požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Na následujícím obrázku čísla reprezentují tok požadavků v chronologickém pořadí:

![Ověřování aplikací střední vrstvy pomocí AAD z webového rozhraní API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Aplikace střední vrstvy požaduje přístupový token Azure AD s následujícími parametry:  

   * Koncový bod tenanta Azure AD.
   * Media Services identifikátor URI prostředku.
   * Identifikátor URI prostředku pro Media Services REST
   * Hodnoty aplikací Azure AD: ID klienta a tajný kód klienta.

   Pokud chcete získat všechny potřebné hodnoty, přečtěte si téma [přístup Azure Media Services API](./access-api-howto.md).

2. Přístupový token Azure AD se pošle do střední úrovně.
4. Střední vrstva odesílá požadavek do Azure Media REST API s tokenem Azure AD.
5. Střední vrstva vrátí data z Media Services.

### <a name="samples"></a>ukázky

Podívejte se na následující ukázky, které ukazují, jak se připojit pomocí instančního objektu služby Azure AD:

* [Připojit se pomocí REST](media-rest-apis-with-postman.md)  
* [Připojení s využitím Javy](configure-connect-java-howto.md)
* [Připojení s využitím .NET](configure-connect-dotnet-howto.md)
* [Připojení s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojení s využitím Pythonu](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Zásady vytváření názvů

Na názvy prostředků služby Azure Media Services v3 (například prostředky, úlohy, transformace) se vztahují omezení vytváření názvů Azure Resource Manageru. V souladu s Azure Resource Managerem jsou názvy prostředků vždy jedinečné. Jako názvy prostředků tedy můžete použít jakékoli řetězce jedinečného identifikátoru (například identifikátory GUID).

Názvy prostředků Media Services nemůžou zahrnovat: "<", ">", "% &", ":", "&#92;", "?", "/", "*", "+", ".", znak jednoduché uvozovky nebo libovolné řídicí znaky. Všechny ostatní znaky jsou povolené. Maximální délka názvu prostředku je 260 znaků.

Další informace o pojmenování Azure Resource Manager najdete v tématu [požadavky](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) na pojmenování a zásady [vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Názvy souborů/objektů BLOB v rámci assetu

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) a [požadavky na název systému souborů NTFS](/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="long-running-operations"></a>Dlouhodobě běžící operace

Operace označené `x-ms-long-running-operation` v Azure Media Services [soubory Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) jsou dlouhodobě běžící operace. 

Podrobnosti o tom, jak sledovat asynchronní operace Azure, najdete v tématu [asynchronní operace](../../azure-resource-manager/management/async-operations.md).

Media Services má následující dlouhodobě běžící operace:

* [Vytváření živých událostí](/rest/api/media/liveevents/create)
* [Aktualizovat živé události](/rest/api/media/liveevents/update)
* [Odstranit živou událost](/rest/api/media/liveevents/delete)
* [Spustit živou událost](/rest/api/media/liveevents/start)
* [Zastavit Livestream](/rest/api/media/liveevents/stop)

  Použijte `removeOutputsOnStop` parametr k odstranění všech přidružených výstupů živého vysílání při zastavování události.  
* [Resetovat Livestream](/rest/api/media/liveevents/reset)
* [Vytvořit LiveOutput](/rest/api/media/liveevents/create)
* [Odstranit LiveOutput](/rest/api/media/liveevents/delete)
* [Vytvořit StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Aktualizovat StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Odstranit StreamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Spustit StreamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Zastavit StreamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Škálování StreamingEndpoint](/rest/api/media/streamingendpoints/scale)

Po úspěšném odeslání dlouhé operace obdržíte 202 přijatý a musí se dotazovat na dokončení operace s použitím vráceného ID operace.

Článek [sledování asynchronních operací Azure](../../azure-resource-manager/management/async-operations.md) podrobněji vysvětluje, jak sledovat stav asynchronních operací Azure prostřednictvím hodnot vrácených v odpovědi.

Pro danou živou událost nebo jakýkoli z přidružených výstupů živého vysílání je podporována pouze jedna dlouhodobě běžící operace. Po spuštění se musí dlouho běžící operace dokončit před spuštěním následné dlouhotrvající operace na stejném Livestream nebo jakýchkoli přidružených živých výstupech. U živých událostí s více živými výstupy musíte očekávat dokončení dlouhotrvající operace na jednom aktivním výstupu před aktivací dlouhotrvající operace na jiném živém výstupu. 

## <a name="sdks"></a>Sady SDK

> [!NOTE]
> Sady SDK Azure Media Services V3 nejsou zaručeny jako bezpečné pro přístup z více vláken. Při vývoji aplikace s více vlákny byste měli přidat vlastní logiku synchronizace vláken pro ochranu klienta nebo použití nového objektu AzureMediaServicesClient na vlákno. Měli byste také dbát na problémy s více vlákny, které přináší volitelné objekty poskytované vaším kódem, klientovi (například instance HttpClient v rozhraní .NET).

|Sada SDK|Odkaz|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Referenční informace k .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Referenční informace k Javě](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referenční informace k Pythonu](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referenční informace k Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referenční informace k jazyku Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Viz také

- [EventGrid .NET SDK zahrnující události Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definice událostí Media Services](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Průzkumník Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) je nástroj dostupný pro zákazníky s Windows, kteří se chtějí dozvědět o Media Services. AMSE je aplikace WinForms/C#, která odesílá, stahuje, kóduje, streamuje VOD a živý obsah pomocí Media Services. Nástroj AMSE je určen pro klienty, kteří chtějí testovat Media Services bez psaní kódu. Kód AMSE je k dispozici jako prostředek pro zákazníky, kteří chtějí vyvíjet pomocí Media Services.

AMSE je otevřený zdrojový projekt. Podpora je poskytována komunitou (mohou být hlášeny problémy) https://github.com/Azure/Azure-Media-Services-Explorer/issues) . Tento projekt přijal [pravidla chování pro Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Další informace najdete v tématu [Nejčastější dotazy k kódu](https://opensource.microsoft.com/codeofconduct/faq/) , nebo kontaktujte opencode@microsoft.com s dalšími dotazy nebo komentáři.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrování, řazení, stránkování Media Services entit

Viz téma [filtrování, řazení, stránkování Azure Media Services entit](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="see-also"></a>Viz také

Pokud chcete získat všechny potřebné hodnoty, přečtěte si téma [přístup Azure Media Services API](./access-api-howto.md).

## <a name="next-steps"></a>Další kroky

* [Připojení k Media Services pomocí Java](configure-connect-java-howto.md)
* [Připojení k Media Services pomocí .NET](configure-connect-dotnet-howto.md)
* [Připojení k Media Services s využitím Node.js](configure-connect-nodejs-howto.md)
* [Připojení k Media Services pomocí Pythonu](configure-connect-python-howto.md)
