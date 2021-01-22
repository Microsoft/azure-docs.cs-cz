---
title: Media Services Operations REST API Overview | Microsoft Docs
description: Rozhraní API "Media Services Operations REST" se používá k vytváření úloh, assetů, živých kanálů a dalších prostředků v účtu Media Services. Tento článek poskytuje přehled REST API Azure Media Services V2.
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
ms.openlocfilehash: f48a01bb81829ff2bc10b4db1ed543382f992b58
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696223"
---
# <a name="media-services-operations-rest-api-overview"></a>Přehled Media Servicesch operací REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Rozhraní **REST API služby Media Services Operations** se používá k vytváření úloh, prostředků, živých kanálů a dalších prostředků v účtu Media Services. Další informace naleznete v tématu [Media Services operations REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services poskytuje REST API, který přijímá formát JSON nebo Atom + Pub. Media Services REST API vyžadují konkrétní hlavičky protokolu HTTP, které musí každý klient odeslat při připojení k Media Services a také sadu volitelných hlaviček. V následujících částech jsou popsány hlavičky a příkazy HTTP, které můžete použít při vytváření požadavků a příjem odpovědí z Media Services.

Ověřování u Media Services REST API se provádí prostřednictvím Azure Active Directory ověřování, které je uvedené v článku [použití ověřování Azure AD pro přístup k rozhraní API pro Azure Media Services s REST](media-services-rest-connect-with-aad.md) .

## <a name="considerations"></a>Požadavky

Při použití REST platí následující požadavky.

* Při dotazování entit existuje limit 1000 entit vrácených v jednom okamžiku, protože veřejné REST v2 omezuje výsledky dotazu na 1000 výsledků. Musíte použít **Skip** a **převzít** (rozhraní .NET)/ **horní** (REST), jak je popsáno v [tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a v [tomto příkladu REST API](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Při použití formátu JSON a určení použití klíčového slova **__metadata** v žádosti (například pro odkazování na propojený objekt) je nutné nastavit [podrobný formát](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) **Accept** on JSON (viz následující příklad). OData nerozumí vlastnosti **__metadata** v žádosti, pokud ji nenastavíte na Verbose.  

    ```console
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
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardní hlavičky požadavků HTTP, které podporuje Media Services
Pro každé volání, které provedete v Media Services, je k dispozici sada požadovaných hlaviček, kterou musíte zahrnout do žádosti, a také sadu volitelných hlaviček, které byste mohli chtít zahrnout. Následující tabulka uvádí seznam požadovaných hlaviček:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Autorizace |Bearer |Držitelem je jediný přijatý autorizační mechanismus. Hodnota musí také zahrnovat přístupový token, který poskytuje Azure Active Directory. |
| x-MS-Version |Decimal |2,17 (nebo novější verze)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Vzhledem k tomu, že Media Services používá ke zveřejnění rozhraní REST API protokol OData, hlavičky DataServiceVersion a MaxDataServiceVersion by měly být zahrnuté do všech požadavků. Pokud nejsou, ale v současné době Media Services předpokládá, že použitá hodnota DataServiceVersion je 3,0.
> 
> 

Následuje sada volitelných hlaviček:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Date (Datum) |RFC 1123 datum |Časové razítko požadavku |
| Přijmout |Typ obsahu |Požadovaný typ obsahu pro odpověď, například následující:<p> -Application/JSON; OData = verbose<p> – Application/Atom + XML<p> Odpovědi mohou mít jiný typ obsahu, například načtení objektu blob, kde úspěšná odpověď obsahuje datový proud blobu jako datovou část. |
| Accept-Encoding |GZIP, uprostřed zúžené |Kódování GZIP a DEFLATE, pokud je to možné. Poznámka: u velkých prostředků Media Services může tuto hlavičku ignorovat a vracet nekomprimovaná data. |
| Accept-Language |"en", "ES" atd. |Určuje preferovaný jazyk pro odpověď. |
| Accept-Charset |Typ znakové sady jako UTF-8 |Výchozí hodnota je UTF-8. |
| X-HTTP-metoda |HTTP – metoda |Umožňuje klientům nebo branám firewall, které nepodporují metody HTTP, jako je PUT nebo DELETE, používat tyto metody, tunelování prostřednictvím volání GET. |
| Typ obsahu |Typ obsahu |Typ obsahu textu žádosti v požadavcích PUT nebo POST |
| klient-požadavek-ID |Řetězec |Hodnota definovaná volajícím, která identifikuje daný požadavek. Je-li tento parametr zadán, bude tato hodnota ve zprávě odpovědi uvedena jako způsob mapování požadavku. <p><p>**Důležité upozornění**<p>Hodnoty by měly být omezené na 2096b (2k). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standardní hlavičky HTTP odpovědi, které podporuje Media Services
Následuje sada hlaviček, které mohou být vráceny v závislosti na prostředku, který požadujete, a akci, kterou jste chtěli provést.

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| ID žádosti |Řetězec |Jedinečný identifikátor pro aktuální operaci, vygenerovala se služba. |
| klient-požadavek-ID |Řetězec |Identifikátor určený volajícím v původní žádosti, pokud je k dispozici. |
| Date (Datum) |RFC 1123 datum |Datum a čas zpracování žádosti. |
| Typ obsahu |Různé |Typ obsahu textu odpovědi |
| Kódování obsahu |Různé |Gzip nebo deflate, podle potřeby. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardní příkazy HTTP podporované Media Services
Následuje úplný seznam příkazů HTTP, které lze použít při provádění požadavků HTTP:

| Příkaz | Popis |
| --- | --- |
| GET |Vrátí aktuální hodnotu objektu. |
| POST |Vytvoří objekt na základě poskytnutých dat nebo odešle příkaz. |
| PUT |Nahradí objekt nebo vytvoří pojmenovaný objekt (je-li k dispozici). |
| DELETE |Odstraní objekt. |
| SLOUČENÍ |Aktualizuje existující objekt se změnami pojmenovaných vlastností. |
| HEAD |Vrátí metadata objektu pro odpověď GET. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Zjištění a procházení modelu entity Media Services
Aby bylo možné Media Services entit lépe zjistitelné, lze použít operaci $metadata. Umožňuje načíst všechny platné typy entit, vlastnosti entit, přidružení, funkce, akce a tak dále. Přidáním operace $metadata na konec Media Services koncového bodu REST API můžete získat přístup k této službě zjišťování.

 /API/$metadata.

Pokud chcete zobrazit metadata v prohlížeči nebo nezahrnovat do žádosti hlavičku x-MS-Version, měli byste připojit "? API-Version = 2. x" na konec identifikátoru URI.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Ověřování pomocí Media Services REST pomocí Azure Active Directory

Ověřování na REST API se provádí prostřednictvím Azure Active Directory (AAD).
Podrobnosti o tom, jak získat požadované podrobnosti o ověřování pro váš účet Media Services z webu Azure Portal, najdete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Podrobnosti o psaní kódu, který se připojuje k REST API pomocí ověřování Azure AD, najdete v článku [použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Další kroky
Informace o tom, jak používat ověřování Azure AD s Media Services REST API, najdete v tématu [použití ověřování Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
