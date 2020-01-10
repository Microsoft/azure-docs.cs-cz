---
title: Resetování dovedností (API-Version = 2019-050 -06-Preview)
titleSuffix: Azure Cognitive Search
description: Náhled REST API použít pro přírůstkové obohacení, pokud potřebujete úplné nebo částečné přepracování dovednosti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 4557e966c431eca31aa7577bebc75caae9365dc9
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832157"
---
# <a name="reset-skills-api-version2019-05-06-preview"></a>Resetování dovedností (API-Version = 2019-05 -06-Preview)

> [!IMPORTANT] 
> Přírůstkové obohacení je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Požadavek na **resetování dovedností** určuje, které dovednosti se mají zpracovat u dalšího spuštění indexeru. U indexerů, které mají povoleno ukládání do mezipaměti, můžete explicitně požadovat zpracování aktualizací dovedností, které indexer nemůže zjistit. Pokud například provedete externí změny, jako je revize vlastní dovednosti, můžete toto rozhraní API použít k opětovnému spuštění dovednosti. Výstupy, jako je znalostní báze úložiště nebo vyhledávací index, se aktualizují pomocí opakovaně použitelných dat z mezipaměti a nového obsahu na aktualizovanou dovednost.

Existující [dovednosti](https://docs.microsoft.com/rest/api/searchservice/create-skillset) můžete obnovit pomocí HTTP PUT zadáním názvu dovednosti, který se má aktualizovat na identifikátoru URI požadavku. Je nutné použít **rozhraní API-Version = 2019-05 -06-Preview** na žádosti.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```  

Případně použijte POST a vložte název indexeru do těla žádosti:

```http
POST https://[service name].search.windows.net/indexers/resetskills?api-version=[api-version]  
api-key: [admin key]  
```  

## <a name="request-headers"></a>Hlavičky požadavku  

 V následující tabulce jsou popsány povinné a volitelné hlavičky požadavků.  

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Nastavte tuto hodnotu na `application/json`|  
|*klíč rozhraní API:*|Povinná hodnota. `api-key` slouží k ověření požadavku na vyhledávací službu. Je to řetězcová hodnota, která je jedinečná pro vaši službu. Požadavek na **resetování dovednosti** musí zahrnovat záhlaví `api-key` nastavené na klíč správce (na rozdíl od klíče dotazu).|  

K vytvoření adresy URL požadavku je také potřeba název služby. Název služby a `api-key` můžete získat ze stránky přehled služby v Azure Portal. Další informace najdete v tématu [Vytvoření služby Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-create-service-portal) pro nápovědu k navigaci na stránce.  

## <a name="request-body-syntax"></a>Syntaxe textu žádosti  

Text požadavku je pole názvů dovedností.

```json
{   
    "skillNames" : ["<SKILL-1>", "SKILL-n"]
}  
```

## <a name="response"></a>Odpověď  

Stavový kód: 204 bez obsahu pro úspěšnou odpověď. 

## <a name="see-also"></a>Další informace najdete v tématech

+ [Hledat rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice)
+ [Stavové kódy HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)  
+ [Přehled rozšíření AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro)
+ [Konfigurace ukládání do mezipaměti a přírůstkové rozšíření](search-howto-incremental-index.md)
+ [Přírůstkové obohacení](cognitive-search-incremental-indexing-conceptual.md)