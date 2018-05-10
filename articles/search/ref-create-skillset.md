---
title: Vytvoření Skillset (REST api-version = 2017-11-11-Preview) – služba Azure Search | Microsoft Docs
description: Skillset je kolekce kognitivní znalosti, které tvoří kanál obohacení.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a5277d4137ede5fe6dacf993413eefd7c9e46ad4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-skillset-api-version2017-11-11-preview"></a>Vytvoření Skillset (api-version = 2017-11-11-Preview)

**Platí pro:** api-version-2017-11-11-Preview

Skillset je kolekce kognitivní dovednosti použitý pro zpracování přirozeného jazyka a dalších transformace. Dovednosti zahrnují pojmenované entity extrakce, klíče frázi extrakce, rozdělování text do logických stránkách, mimo jiné.

Pokud chcete použít skillset, odkaz v indexer Azure Search a pak spusťte indexeru importovat data, vyvolání transformace a obohacení a mapování na index pole výstup. Skillset je zdrojů vysoké úrovně, ale je funkční pouze v rámci zpracování indexer. Jako prostředek vysoké úrovně můžete navrhnout skillset jednou a poté odkazovat ve více indexery. 

Skillset je vyjádřena v Azure Search pomocí HTTP PUT nebo požadavek POST. Text žádosti PUT, je schématu JSON, která určuje, které dovednosti jsou vyvolány. Dovednosti jsou zřetězen dohromady prostřednictvím přidružení vstupy a výstupy, kde bude výstup transformace jeden vstup do jiného.

Skillset musí mít alespoň jeden znalostí. Neexistuje žádné teoretické omezení na maximální počet znalosti, ale tři až pět je obvyklé konfigurace.  


> [!NOTE]
> Kognitivní vyhledávání je ve verzi Public Preview a využití jeho dovedností se momentálně nabízí zdarma. Cenu těchto funkcí oznámíme později.

```http  
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```  

## <a name="request"></a>Žádost  
 Je požadován pro všechny žádosti o služby protokol HTTPS. **Vytvořit Skillset** požadavek se dá vytvořit pomocí metody PUT, s názvem skillset jako část adresy URL. Pokud skillset neexistuje, vytvoří se. Pokud již existuje, je aktualizován na novou definici. Všimněte si, že můžete DÁT pouze jeden skillset najednou.  

 Název skillset musí splňovat následující požadavky:

- Být malými písmeny
- Začínat a končit písmenem nebo číslicí
- Mít žádné lomítka ani tečky
- Mít méně než 128 znaků. 

Po spuštění skillset název s písmenem nebo číslicí, může obsahovat zbytek název jakékoli písmeno, čísla a pomlčky, dokud nejsou po sobě jdoucí pomlčky.  

**Verze rozhraní api** parametr je povinný. Je k dispozici pouze verze `2017-11-11-Preview`. V tématu [verze rozhraní API ve službě Azure Search](https://go.microsoft.com/fwlink/?linkid=834796) seznam všech verzí. 


### <a name="request-headers"></a>Hlavičky požadavku  

 Následující tabulka popisuje hlavičky žádosti požadované a volitelné.  

|Hlavička požadavku|Popis|  
|--------------------|-----------------|  
|*Content-Type:*|Povinná hodnota. Tuto možnost nastavíte na `application/json`|  
|*klíč rozhraní API:*|Povinná hodnota. `api-key` Se používá k ověření požadavku na vaši službu vyhledávání. Je řetězcovou hodnotu, jedinečné pro vaši službu. **Vytvořit Skillset** musí zahrnovat požadavek `api-key` záhlaví nastavit klíče správce (na rozdíl od klíč dotazů).|  

Musíte také název služby můžete vytvořit adresu URL požadavku. Můžete získat název služby a `api-key` z řídicího panelu služby na portálu Azure. V tématu [vytvoření služby Azure Search na portálu](search-create-service-portal.md) nápovědu navigace stránky.  

### <a name="request-body-syntax"></a>Syntaxe požadavku textu  

Text žádosti obsahuje definici skillset, který se skládá z jednoho nebo více určenými plně schopností, jakož i volitelné parametry název a popis.  

Syntaxe pro vytváření struktury datová část požadavku je následující. Ukázková žádost poskytuje později v tomto článku a také v [jak definovat skillset](cognitive-search-defining-skillset.md).  

```
{   
    "name" : "Required for POST, optional for PUT. Friendly name of the skillset",  
    "description" : "Optional. Anything you want, or null",  
    "Skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",  
}  
```

### <a name="request-example"></a>Příklad požadavku
 Následující příklad vytvoří skillset, použít pro rozšíření kolekce finanční dokumenty.

```http
PUT https://[servicename].search.windows.net/skillsets/financedocenricher?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

Text žádosti je dokument JSON. Tato konkrétní skillset používá dva dovednosti asynchronně, nezávisle na nástroji zpracování podstaty `contents` jako dvě různé transformace. Alternativně můžete nastavit výstupní jeden transformace jako vstup jiného. Další informace najdete v tématu [jak definovat skillset](cognitive-search-defining-skillset.md).

```json
{
  "name": "financedocenricher",
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ]
}
```

## <a name="response"></a>Odpověď  

 Pro úspěšné žádosti by měl zobrazit stavový kód "201 – vytvořeno".  

 Ve výchozím nastavení bude obsahovat text odpovědi JSON pro definici skillset, který byl vytvořen. Ale pokud je Prefer hlavičky žádosti nastavit vrátit = minimální, text odpovědi bude prázdný, a bude kód stavu úspěch "204 žádný obsah" místo "201 – vytvořeno". To platí bez ohledu na to, jestli PUT nebo POST se používá k vytvoření skillset.   

## <a name="see-also"></a>Další informace najdete v tématech

+ [Přehled kognitivní vyhledávání](cognitive-search-concept-intro.md)
+ [Rychlý úvod: Zkuste kognitivní vyhledávání](cognitive-search-quickstart-blob.md)
+ [Kurz: Obohacená známým indexování objektů Azure BLOB](cognitive-search-tutorial-blob.md)
+ [Jak definovat skillset](cognitive-search-defining-skillset.md)
+ [Mapování polí](cognitive-search-output-field-mapping.md)
+ [Jak definovat vlastní rozhraní](cognitive-search-custom-skill-interface.md)
+ [Příklad: vytvoření vlastní dovedností](cognitive-search-create-custom-skill-example.md)
+ [Předdefinované sklls](cognitive-search-predefined-skills.md)