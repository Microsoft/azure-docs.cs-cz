---
title: Přehled rozhraní REST API služby Media Services operace | Dokumentace Microsoftu
description: Přehled rozhraní REST API pro Media Services
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
ms.date: 10/29/2018
ms.author: juliako;johndeu
ms.openlocfilehash: 38310ce64335e03c6728092bf1b8ce4752740a83
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233459"
---
# <a name="media-services-operations-rest-api-overview"></a>Přehled rozhraní REST API služby Media Services operace
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

**Media Services operace REST** rozhraní API slouží k vytvoření úlohy, prostředky, živých kanálů a dalších prostředků v účtu Azure Media Services. Další informace najdete v tématu [referenční dokumentace rozhraní API služby Media Services operace REST](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Služba Media Services poskytuje rozhraní REST API, která přijímá JSON nebo atom + pub formátu XML. REST API služby Media Services vyžaduje konkrétní hlavičky protokolu HTTP, které každý klient musí odesílat při připojování ke službě Media Services, jakož i sadu volitelná záhlaví. Následující části popisují záhlaví a příkazy HTTP, můžete použít při vytváření žádostí o a příjem odpovědí ze služby Media Services.

Ověřování rozhraní REST API služby Media se provádí pomocí ověřování Azure Active Directory, který je popsaný v článku [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Požadavky

Při používání REST, platí následující aspekty.

* Při dotazování entit, platí limit 1 000 entit najednou vrátit, protože veřejné v2 REST omezuje výsledky dotazu na 1000 výsledky. Je třeba použít **přeskočit** a **trvat** (.NET) / **horní** (REST), jak je popsáno v [v tomto příkladu .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) a [toto rozhraní REST API Příklad](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* Když pomocí formátu JSON a určení pro použití **__metadata** – klíčové slovo v požadavku (například k odkazu na odkazovaný objekt) je nutné nastavit **přijmout** záhlaví [JSON podrobný formát](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)(viz následující příklad). OData nerozumí **__metadata** vlastnost v požadavku, pokud ho nastavíte na verbose.  
  
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

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standardní hlavičky požadavků HTTP podporovaných službou Media Services
Pro každé volání, které provedete do Media Services je sada požadované hlavičky, které je třeba zahrnout vaši žádost a také sadu volitelná záhlaví můžete chtít zahrnout. Následující tabulka uvádí požadované hlavičky:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Autorizace |Nosiče |Nosiče je pouze přijaté autorizační mechanismus. Hodnota musí obsahovat také přístupový token poskytovaný službou Azure Active Directory. |
| x-ms-version |Decimal |2.17 (nebo nejnovější verze)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Protože Media Services využívá k tomu jeho rozhraní REST API OData, záhlaví DataServiceVersion a MaxDataServiceVersion, měly by být součástí všech požadavků. ale pokud nejsou, pak aktuálně Media Services se předpokládá, že DataServiceVersion používá hodnotu 3.0.
> 
> 

Následuje sadu volitelné hlavičky:

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| Datum |RFC 1123 datum |Časové razítko požadavku |
| Přijmout |Typ obsahu |Požadovaný typ obsahu pro odpověď, jako je následující:<p> -application/json; odata = verbose<p> -application/atom + xml<p> Odpovědi může mít jiný typ obsahu, jako je například načtení objektu blob, kde úspěšná odpověď obsahuje objekt blob datového proudu jako datovou část. |
| Přijmout kódování |GZIP, deflate |GZIP a DEFLATE kódování, pokud se používá. Poznámka: Pro velké prostředky, Media Services může ignorovat toto záhlaví a vrátit nekomprimované data. |
| Přijměte jazyka |"en", "es" a tak dále. |Určuje upřednostňovaný jazyk pro odpověď. |
| Přijměte znaková sada |Znaková sada typ jako "UTF-8" |Výchozí hodnota je UTF-8. |
| X-HTTP-Method |Metoda HTTP |Umožňuje klientům NAT nebo branami firewall, které nepodporují metod HTTP PUT nebo DELETE používat tyto metody, tunelové propojení prostřednictvím volání GET. |
| Typ obsahu |Typ obsahu |Požádá o obsahu typ textu žádosti PUT nebo POST. |
| Client-request-id |Řetězec |Volající definované hodnotu, která identifikuje daného požadavku. Je-li zadána, tato hodnota se zahrnou ve zprávě s odpovědí jako způsob mapování požadavku. <p><p>**Důležité upozornění**<p>Hodnoty by měly být omezené na 2096b (tis. 2). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Hlavičky standardních odpovědí HTTP podporovaných službou Media Services
Následuje sadu hlavičky, které mohou být vráceny, v závislosti na prostředek, který se požaduje a akci, kterou máte v úmyslu provést.

| Hlavička | Typ | Hodnota |
| --- | --- | --- |
| id požadavku |Řetězec |Jedinečný identifikátor pro aktuální operaci, vygeneruje služby. |
| Client-request-id |Řetězec |Identifikátor určený volajícím v původní požadavek, pokud jsou k dispozici. |
| Datum |RFC 1123 datum |Datum/čas zpracování žádosti. |
| Typ obsahu |Různé |Typ obsahu těla odpovědi. |
| Kódování obsahu |Různé |Gzip nebo deflate podle potřeby. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standardní příkazy HTTP podporovaných službou Media Services
Následuje úplný seznam příkazů HTTP, které se dá použít při vytváření HTTP žádosti:

| Příkaz | Popis |
| --- | --- |
| GET |Vrátí aktuální hodnotu objektu. |
| POST |Vytvoří objekt, na základě dat poskytuje nebo odešle příkaz. |
| PUT |Nahradí objekt nebo vytvoří objekt s názvem (Pokud se používá). |
| DELETE |Odstraní objekt. |
| SLOUČENÍ |Aktualizuje existující objekt změn s názvem vlastnosti. |
| HLAVNÍ |Vrátí metadata objektu pro získání odpovědi. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Vyhledat a procházet entity model služby Media Services
Chcete-li zjistitelnější entity Media Services, je možné $metadata operace. Umožňuje načíst všechny typy entit platný, vlastností entity, přidružení, funkce, akce a tak dále. Přidáním $metadata operaci za účelem váš koncový bod REST API služby Media Services, dostanete tuto službu zjišťování.

 /API/$ metadata.

By měl připojit "? api-version=2.x" na konec identifikátoru URI, chcete-li v prohlížeči zobrazit metadata, nebo nesmí obsahovat hlavičku x-ms-version pro vaši žádost.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Ověřování pomocí REST Media Services pomocí služby Azure Active Directory

Ověřování pomocí rozhraní REST API se provádí prostřednictvím Azure Active prodloužil platnost.
Podrobnosti o tom, jak získat podrobnosti požadovaného ověření pro váš účet Media Services z webu Azure Portal najdete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

Informace o psaní kódu, který se připojuje k rozhraní REST API pomocí ověřování Azure AD, najdete v článku [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Další postup
Informace o používání ověřování Azure AD pomocí rozhraní REST API pro Media Services, najdete v článku [ověřování pomocí služby Azure AD pro přístup k rozhraní API Azure Media Services pomocí REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

