---
title: Media Services operations REST API overview | Dokumenty společnosti Microsoft
description: Rozhraní API "Media Services Operations REST" se používá k vytváření úloh, datových zdrojů, živých kanálů a dalších prostředků v účtu Mediálních služeb. Tento článek obsahuje přehled rozhraní REST api služby Azure Media Services v2.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 597839f633ed2b925b86c5f859a0fb2d3b64dd59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773663"
---
# <a name="media-services-operations-rest-api-overview"></a>Media Services operace REST API přehled 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Rozhraní **REST API pro operace mediálních služeb** se používá k vytváření úloh, datových zdrojů, živých kanálů a dalších prostředků v účtu Mediálních služeb. Další informace naleznete v [tématu Media Services Operations REST API reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services poskytuje rozhraní REST API, které přijímá formát XML JSON nebo atom+pub. Rozhraní REST API služby Media Services vyžaduje specifické hlavičky PROTOKOLU HTTP, které musí každý klient odeslat při připojení ke službě Media Services, a také sadu volitelných záhlaví. Následující části popisují záhlaví a slovesa HTTP, která můžete použít při vytváření požadavků a přijímání odpovědí ze služby Media Services.

Ověřování rozhraní REST api mediálních služeb se provádí prostřednictvím ověřování Azure Active Directory, které je nastíněno v článku [Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Požadavky

Následující úvahy platí při použití REST.

* Při dotazování entit je limit 1000 entit vrácených najednou, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Musíte použít **Skip** and **Take** (.NET)/ **top** (REST), jak je popsáno v [tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [v tomto příkladu rozhraní REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Při použití JSON a určení použití **__metadata** klíčové slovo v požadavku (například odkaz na propojený objekt), musíte nastavit **Accept** záhlaví [do formátu JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (viz následující příklad). Odata nerozumí **vlastnosti __metadata** v požadavku, pokud ji nenastavíte na podrobné.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardní hlavičky požadavků HTTP podporované službou Media Services
Pro každý hovor, který provedete do služby Media Services, je sada požadovaných záhlaví, které musíte zahrnout do požadavku a také sadu volitelných záhlaví, které můžete chtít zahrnout. V následující tabulce jsou uvedena požadovaná záhlaví:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Autorizace |Bearer |Nosič je jediný přijatý autorizační mechanismus. Hodnota musí také obsahovat přístupový token poskytovaný službou Azure Active Directory. |
| x-ms-verze |Desetinné číslo |2.17 (nebo nejnovější verze)|
| Verze DataServiceVersion |Desetinné číslo |3.0 |
| Verze MaxDataService |Desetinné číslo |3.0 |

> [!NOTE]
> Vzhledem k tomu, že Media Services používá OData k vystavení svých rest API, hlavičky DataServiceVersion a MaxDataServiceVersion by měly být zahrnuty do všech požadavků; pokud tomu tak však není, pak aktuálně Media Services předpokládá, že dataServiceVersion hodnota v použití je 3.0.
> 
> 

Následuje sada volitelných záhlaví:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Datum |RFC 1123 datum |Časové razítko žádosti |
| Accept |Typ obsahu |Požadovaný typ obsahu pro odpověď, například následující:<p> -application/json;odata=verbose<p> - aplikace/atom+xml<p> Odpovědi mohou mít jiný typ obsahu, například načítání objektů blob, kde úspěšná odpověď obsahuje datový proud objektu blob jako datovou část. |
| Přijmout kódování |Gzip, deflaci |Kódování GZIP a DEFLATE, je-li k dispozici. Poznámka: U velkých prostředků může mediální služba tuto hlavičku ignorovat a vrátit nekomprimovaná data. |
| Accept-Language |"en", "es" a tak dále. |Určuje upřednostňovaný jazyk odpovědi. |
| Přijmout-Charset |Typ znakové sady jako "UTF-8" |Výchozí hodnota je UTF-8. |
| Metoda X-HTTP |Metoda HTTP |Umožňuje klientům nebo bránovým bráněm, které nepodporují metody HTTP jako PUT nebo DELETE používat tyto metody, tunelované prostřednictvím volání GET. |
| Typ obsahu |Typ obsahu |Typ obsahu textu požadavku v požadavcích PUT nebo POST. |
| id požadavku klienta |Řetězec |Volající definovaná hodnota, která identifikuje daný požadavek. Pokud je zadána, bude tato hodnota zahrnuta do zprávy odpovědi jako způsob mapování požadavku. <p><p>**Důležité**<p>Hodnoty by měly být omezeny na 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardní hlavičky odpovědí HTTP podporované službou Media Services
Následuje sada záhlaví, které mohou být vráceny na vás v závislosti na prostředek, který jste požadovali a akce, kterou jste chtěli provést.

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| id požadavku |Řetězec |Jedinečný identifikátor pro aktuální operaci, služba generována. |
| id požadavku klienta |Řetězec |Identifikátor určený volajícím v původním požadavku, pokud je k dispozici. |
| Datum |RFC 1123 datum |Datum a čas, kdy byl požadavek zpracován. |
| Typ obsahu |Různé |Typ obsahu těla odezvy. |
| Kódování obsahu |Různé |Gzip nebo deflaci, podle potřeby. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardní slovesa HTTP podporovaná službou Media Services
Následuje úplný seznam sloves HTTP, které lze použít při vytváření požadavků HTTP:

| Příkaz | Popis |
| --- | --- |
| GET |Vrátí aktuální hodnotu objektu. |
| POST |Vytvoří objekt na základě poskytnutých dat nebo odešle příkaz. |
| PUT |Nahradí objekt nebo vytvoří pojmenovaný objekt (pokud je to možné). |
| DELETE |Odstraní objekt. |
| Sloučit |Aktualizuje existující objekt s pojmenovanými změnami vlastností. |
| HEAD |Vrátí metadata objektu pro odpověď GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Zjišťování a procházení modelu entity Mediálních služeb
Chcete-li, aby entity mediálních služeb byly více zjistitelné, lze použít $metadata operaci. Umožňuje načíst všechny platné typy entit, vlastnosti entit, přidružení, funkce, akce a tak dále. Přidáním $metadata operace na konec koncového bodu rozhraní REST ROZHRANÍ MEDIA Services, můžete získat přístup k této službě zjišťování.

 /api/$metadata.

Pokud chcete zobrazit metadata v prohlížeči nebo nezahrnovat hlavičku verze x-ms v požadavku, měli byste připojit "?api-version=2.x" na konec identifikátoru URI.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Ověření pomocí služby Media Services REST pomocí služby Azure Active Directory

Ověřování v rozhraní REST API se provádí prostřednictvím Azure Active Directory (AAD).
Podrobnosti o tom, jak získat požadované podrobnosti o ověřování pro váš účet Mediální služby z webu Azure Portal, najdete [v tématu Přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Podrobnosti o psaní kódu, který se připojuje k rozhraní REST API pomocí ověřování Azure AD, najdete v článku [Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Další kroky
Informace o tom, jak používat ověřování Azure AD s rozhraním API MEDIA Services REST, najdete [v tématu Použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services s REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

