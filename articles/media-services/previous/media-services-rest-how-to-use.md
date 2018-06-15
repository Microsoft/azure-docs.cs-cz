---
title: Přehled rozhraní REST API služby Media Services operations | Microsoft Docs
description: Přehled Media Services REST API
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;johndeu
ms.openlocfilehash: af83e876802b176d4e097535d45df91e8a986dfb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790451"
---
# <a name="media-services-operations-rest-api-overview"></a>Přehled rozhraní REST API operations Media Services
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

**Media Services operace REST** rozhraní API slouží k vytvoření úlohy, prostředky, v případě živých kanálů a další prostředky v účtu Media Services. Další informace najdete v tématu [Media Services operace REST API odkaz](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Služba Media Services poskytuje rozhraní REST API, který přijímá JSON nebo atom + pub formátu XML. Media Services REST API vyžaduje konkrétní hlavičky protokolu HTTP, které každý klient musí odesílat při připojování k Media Services, jakož i sadu hlaviček volitelné. Následující části popisují hlavičky a příkazy HTTP, můžete použít při vytváření žádostí o a příjem odpovědí ze služby Media Services.

Ověřování rozhraní API REST Serivces média se provádí pomocí ověřování Azure Active Directory, které je popsané v článku [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services se zbytkem](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Požadavky

Při používání REST, platí následující aspekty.

* Při dotazování entity, existuje omezení 1000 entit vrátí najednou, protože veřejné v2 REST omezí výsledky dotazu a 1000 výsledky. Budete muset použít **přeskočit** a **trvat** (.NET) nebo **horní** (REST), jak je popsáno v [v tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [v tomto příkladu REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Při použití formátu JSON a určení pro použití **__metadata** – klíčové slovo v požadavku (například na referenční dokumentace propojeného objektu), je nutné nastavit **přijmout** hlavičky k [JSON podrobný formát](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(viz následující příklad). OData nerozumí **__metadata** vlastnost v požadavku, pokud je nastavena na podrobné.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardní hlavičky požadavku HTTP podporovaných službou Media Services
Pro každé volání, které provedete ve službě Media Services je sada požadované hlavičky, které je nutné zahrnout vaši žádost a také sadu volitelné záhlaví může chcete zahrnout. Následující tabulka uvádí požadované hlavičky:

| Záhlaví | Typ | Hodnota |
| --- | --- | --- |
| Autorizace |Nosiče |Nosiče se jedná o jediné autorizační přijatý mechanismus. Hodnota musí obsahovat také přístupový token poskytovaný službou Azure Active Directory. |
| x-ms-version |Decimal |2.17 (nebo nejnovější verze)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Protože Media Services používá ke zveřejnění jeho rozhraní REST API OData, DataServiceVersion a MaxDataServiceVersion hlavičky by měl být součástí všechny požadavky; ale pokud tomu tak není, pak aktuálně Media Services předpokládá, že hodnota DataServiceVersion používán je 3.0.
> 
> 

Toto je sada volitelné hlavičky:

| Záhlaví | Typ | Hodnota |
| --- | --- | --- |
| Datum |RFC 1123 datum |Časové razítko požadavku |
| Přijmout |Typ obsahu |Požadovaný typ obsahu pro odpověď, například následující:<p> -application/json; odata = verbose<p> -application/atom + xml<p> Odpovědí může mít jiný typ obsahu, jako je například načtení objektu blob, kde úspěšná odpověď obsahuje objekt blob datového proudu jako datovou část. |
| Přijměte kódování |GZIP, deflate |GZIP a DEFLATE kódování, v případě potřeby. Poznámka: Pro velké prostředky Media Services může ignorovat tuto hlavičku a vrátit nekomprimované data. |
| Přijměte jazyk |"en", "es" a tak dále. |Určuje upřednostňovaný jazyk pro odpověď. |
| Accept-Charset |Znaková sada typu like "Znakové sady UTF-8" |Výchozí hodnota je UTF-8. |
| Metoda HTTP X- |Metoda HTTP |Umožňuje klientům nebo brány firewall, které nepodporují metod HTTP PUT nebo odstranění používat tyto metody tunelovým propojením prostřednictvím volání GET. |
| Content-Type |Typ obsahu |Typ obsahu žádosti v žádosti PUT nebo POST obsahu. |
| Client-request-id |Řetězec |Volající definovanou hodnotu, který identifikuje daného požadavku. -Li zadána, bude tato hodnota zahrnuté ve zprávě s odpovědí jako způsob mapování požadavku. <p><p>**Důležité upozornění**<p>Hodnoty musí být limitován 2096b (2 kB). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Hlavičky standardních odpovědí HTTP podporovaných službou Media Services
Následuje sadu hlaviček, které mohou být vráceny vám v závislosti na prostředek, který se požaduje a akci, kterou byste chtěli provést.

| Záhlaví | Typ | Hodnota |
| --- | --- | --- |
| id žádosti |Řetězec |Jedinečný identifikátor pro aktuální operaci, vygeneruje služby. |
| Client-request-id |Řetězec |Identifikátor určeného volající v původní žádost, pokud je k dispozici. |
| Datum |RFC 1123 datum |Datum a čas, požadavek byl zpracován. |
| Content-Type |Je to různé. |Typ obsahu textu odpovědi. |
| Kódování obsahu |Je to různé. |Gzip a deflate podle potřeby. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardní příkazy HTTP podporovaných službou Media Services
Následuje úplný seznam příkazů HTTP, které lze použít při vytváření HTTP požadavků:

| Příkaz | Popis |
| --- | --- |
| GET |Vrací aktuální hodnotu objektu. |
| POST |Vytvoří objekt na základě dat poskytuje nebo odešle příkaz. |
| PUT |Nahradí objekt nebo vytvoří objekt s názvem (v případě potřeby). |
| ODSTRANIT |Odstraní objekt. |
| SLOUČENÍ |Aktualizuje existující objekt s názvem vlastnosti změny. |
| HEAD |Vrátí metadata objektu pro GET odpověď. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Zjišťovat a procházet model entity Media Services
Chcete-li zjistitelná entity Media Services, lze operaci $metadata. Umožňuje načíst všechny typy entit platný, vlastností entity, přidružení, funkce, akce a tak dále. Přidáním $metadata operaci na konec koncový bod služby Media Services REST API, dostanete tuto službu zjišťování.

 /API/$ metadata.

By měl připojit "? api-version=2.x" na konec identifikátor URI, pokud chcete v prohlížeči zobrazit metadata nebo nebudou obsahovat hlavičku x-ms-version ve vaší žádosti.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Ověření se zbytkem Media Services pomocí služby Azure Active Directory

Ověřování na volání rozhraní REST API se provádí pomocí aktivní Directory(AAD) Azure.
Podrobnosti o tom, jak získat podrobnosti požadovaného ověření pro váš účet Media Services na webu Azure Portal najdete v tématu [přístup k Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Informace o psaní kódu, který se připojuje ke službě REST API pomocí ověřování Azure AD, najdete v článku [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services se zbytkem](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Další postup
Další používání ověřování Azure AD s Media Services REST API najdete v tématu [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services se zbytkem](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

