---
title: Definování projekce ve znalostní bázi Knowledge Store
titleSuffix: Azure Cognitive Search
description: Příklady běžných vzorů o tom, jak promítnout obohacené dokumenty do úložiště znalostí pro použití s Power BI nebo Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165511"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Projekce pro znalostní bázi Knowledge Store: jak natvarovat a exportovat obohacení do úložiště znalostí

> [!IMPORTANT] 
> Znalostní databáze je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce ve verzi Preview. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Projekce představují fyzický výraz obohacených dokumentů ve znalostní bázi Knowledge Store. Efektivní používání obohacených dokumentů vyžaduje strukturu. V tomto článku prozkoumáte strukturu i vztahy, naučíte se, jak vytvořit vlastnosti projekce, a jak propojit data napříč typy projekce, které vytvoříte. 

Chcete-li vytvořit projekci, je nutné tvarovat data pomocí Shaper dovednosti pro vytvoření vlastního objektu nebo použití syntaxe vloženého tvaru. Datový tvar obsahuje všechna data, která máte v úmyslu k projektu. Tento dokument obsahuje příklad jednotlivých možností, které můžete použít pro vytvářené projekce.


Existují tři typy projekce:
+ Tabulky
+ Objekty
+ Soubory

Projekce tabulek jsou uloženy v úložišti tabulek Azure. Projekce objektů a souborů jsou zapisovány do úložiště objektů blob, projekce objektů jsou uloženy jako soubory JSON a mohou obsahovat obsah z dokumentu a jakékoliv výstupy nebo obohacení dovedností. Kanál pro obohacení může také extrahovat binární soubory jako obrázky, tyto binární soubory jsou probíhají jako projekce souborů. Když je binární objekt promítnut jako projekce objektu, uloží se pouze metadata, která jsou k němu přidružená, jako objekt BLOB JSON. 

Abychom pochopili průnik mezi vytvářením a projekcí dat, použijeme jako základ pro prozkoumávání různých konfigurací následující dovednosti. Tato dovednosti zpracovává nezpracovaný obsah obrázku a textu. Projekce budou definované z obsahu dokumentu a výstupů dovedností pro scénáře, které chceme podporovat.

Alternativně můžete stáhnout a použít [ukázku REST API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) se všemi voláními v tomto průvodci.

> [!IMPORTANT] 
> Při experimentování s projekcemi je užitečné [nastavit vlastnost mezipaměti indexeru](search-howto-incremental-index.md) , aby se zajistilo řízení nákladů. Pokud mezipaměť indexeru není nastavená, bude se po úpravách projekce znovu obohacen celý dokument. Když je mezipaměť nastavená a jenom aktualizované projekce, dovednosti spuštění pro dříve obohacené dokumenty nevede k žádným Cognitive Services poplatky.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Nyní můžeme přidat objekt `knowledgeStore` a podle potřeby nakonfigurovat projekce pro každý z těchto scénářů. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projekce do tabulek pro scénáře, jako je Power BI

> [!NOTE] 
> Vzhledem k tomu, že znalostní báze je Azure Storage účet, projekce tabulek jsou Azure Storage tabulky a řídí se omezeními úložiště pro tabulky. Další informace najdete v tématu [omezení úložiště tabulek](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Je užitečné znát, že velikost entity nemůže být větší než 1 MB a jedna vlastnost nemůže být větší než 64 KB. Tato omezení vytvářejí v tabulkách dobré řešení pro ukládání velkého počtu malých entit.

Power BI může číst z tabulek a zjišťovat relace na základě klíčů, které vytváří projekce pro znalostní bázi Store. díky tomu jsou tabulky vhodné při vytváření řídicího panelu na obohacených datech. Za předpokladu, že se snažíme vytvořit řídicí panel, kde můžeme vizualizovat klíčové fráze extrahované z dokumentů jako Cloud Wordu, můžeme do dovednosti přidat shaperou dovednost a vytvořit vlastní tvar, který obsahuje podrobné údaje specifické pro dokument a klíčové fráze. Přidejte do dovednosti dovednost Shaper a vytvořte nové rozšíření s názvem ```pbiShape``` v ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Použití dovedností Shaper k vytvoření vlastního obrazce

Vytvořte vlastní tvar, který můžete promítnout do tabulkového úložiště. Bez vlastního tvaru může projekce odkazovat pouze na jeden uzel (jedna projekce na výstup). Vytvoření vlastního obrazce vám umožní agregovat různé prvky do nového logického celku, který se může promítnout jako jedna tabulka, nebo rozdělit a rozdělit do kolekce tabulek. V tomto příkladu vlastní obrazec kombinuje metadata a identifikované entity a klíčové fráze. Objekt se nazývá pbiShape a je nadřazený v rámci `/document`. 

> [!IMPORTANT] 
> Zdrojové cesty pro obohacení musí být ve správném formátu objektů JSON, aby je bylo možné zamítnout. Strom rozkládání může představovat obohacení, které nejsou ve správném formátu JSON, například když je obohacení nadřazené na primitve jako řetězec. Všimněte si, jak jsou `KeyPhrases` a `Entities` zabaleny do platného objektu JSON s `sourceContext`, to je vyžadováno jako `keyphrases` a `entities` je obohacení na primitivních objektech a musí být převedeno na platný formát JSON, aby bylo možné je zabalit.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Přidejte Shaper dovednost, kterou jsme právě definovali do seznamu dovedností v dovednosti. 

Teď, když máme všechna data potřebná pro projekt k tabulkám, aktualizujte objekt knowledgeStore o definice tabulky. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Vlastnost ```storageConnectionString``` nastavte na platný připojovací řetězec účtu úložiště pro obecné účely v2. V tomto scénáři definujeme tři tabulky v objektu projekce nastavením vlastností ```tableName```, ```source``` a ```generatedKeyName```. Dovednosti teď můžete aktualizovat vyvoláním žádosti PUT.

### <a name="slicing"></a>Dělení 

Při zahájení u konsolidovaného obrazce, kde je veškerý obsah, který je nutné promítnout, je v jednom tvaru (nebo uzlu obohacení), nabízí průřezy možnost rozdělit jeden uzel do několika tabulek nebo objektů. Zde je objekt ```pbiShape``` rozdělen do několika tabulek. Funkce dělení na oddíly umožňuje vytáhnout části obrazce, ```keyPhrases``` a ```Entities``` do samostatných tabulek. To je užitečné v případě, že jsou k jednotlivým dokumentům přidruženy různé entity a klíčová fráze. Implicitní vytváření řezů generuje vztah mezi nadřazenými a podřízenými tabulkami, a to pomocí ```generatedKeyName``` v nadřazené tabulce pro vytvoření sloupce se stejným názvem v podřízené tabulce. 

### <a name="naming-relationships"></a>Pojmenovávání vztahů
Vlastnosti ```generatedKeyName``` a ```referenceKeyName``` se používají pro propojení dat napříč tabulkami nebo dokonce napříč typy projekce. Každý řádek v podřízené tabulce nebo projekci má vlastnost ukazující zpátky na nadřazenou položku. Název sloupce nebo vlastnosti v podřízeném objektu je ```referenceKeyName``` z nadřazené položky. Pokud není ```referenceKeyName``` k dispozici, služba ji nastaví jako výchozí pro ```generatedKeyName``` z nadřazené položky. PowerBI spoléhá na tyto vygenerované klíče a zjistí vztahy v tabulkách. Pokud potřebujete sloupec v podřízené tabulce s názvem jinak, nastavte vlastnost ```referenceKeyName``` v nadřazené tabulce. Jedním z příkladů je nastavit ```generatedKeyName``` jako ID v tabulce pbiDocument a ```referenceKeyName``` jako DocumentID. Výsledkem by byl sloupec v tabulkách pbiEntities a pbiKeyPhrases obsahující ID dokumentu s názvem DocumentID.

Uložte aktualizované dovednosti a spusťte indexer. Nyní máte pracovní projekci se třemi tabulkami. Import těchto tabulek do Power BI by měl mít za následek Power BI automatické zjišťování vztahů.

## <a name="projecting-to-objects"></a>Projekce do objektů

Výčnělky objektů nemají stejná omezení jako projekce tabulek, jsou vhodnější pro projekci rozsáhlých dokumentů. V tomto příkladu budeme promítnout celý dokument do projekce objektu. Výčnělky objektů jsou omezeny na jednu projekci v kontejneru.
Pro definování projekce objektu budeme používat pole ```objects``` v projekcích. Nový tvar můžete vygenerovat pomocí dovednosti v Shaper nebo pomocí vloženého tvaru projekce objektu. Přestože příklad tabulky ukázal přístup k vytvoření tvaru a vytváření řezů, tento příklad ukazuje použití vloženého tvaru. Vložené tvarování je schopnost vytvořit nový tvar v definici vstupů do projekce. Vložená tvar vytvoří anonymní objekt, který je totožný s tím, co by měl vytvořit podobný Shaper. Vložení tvarování je užitečné, pokud definujete tvar, který nehodláte znovu použít.
Vlastnost projekce je pole, v tomto příkladu přidáme do pole novou instanci projekce. Aktualizujte definici knowledgeStore pomocí projekce definovaných jako vložené a při použití vložených projekce nemusíte Shaper dovednost.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Projekce souborů

Výčnělky souborů jsou obrázky, které jsou extrahovány ze zdrojového dokumentu nebo výstupů obohacení, které mohou být vycházet z procesu rozšíření. Projekce souborů, podobně jako projekce objektů, jsou implementovány jako objekty BLOB a obsahují bitovou kopii. K vygenerování projekce souboru používáme pole ```files``` v objektu projekce. Tento příklad projekty všechny obrázky extrahované z dokumentu do kontejneru s názvem `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projektování na více typů

Složitější scénář může vyžadovat, abyste promítáte obsah napříč typy projekce. Například pokud potřebujete projektovat některá data jako klíčové fráze a entity do tabulek, uložte výsledky optického rozpoznávání textu a textu rozložení jako objekty a naprojektujte obrázky jako soubory. Tato aktualizace pro dovednosti bude:

1. Vytvoří tabulku, která má řádek pro každý dokument.
2. Vytvořte tabulku související s tabulkou dokumentu s každou klíčovou frází identifikovanou jako řádek v této tabulce.
3. Vytvořte tabulku související s tabulkou dokumentu s každou entitou identifikovanou jako řádek v této tabulce.
4. Vytvořte projekci objektu s textem rozložení pro jednotlivé obrázky.
5. Vytvořte projekci souboru a promítáte každý extrahovaný obrázek.
6. Vytvořte tabulku s křížkem odkazem, která obsahuje odkazy na tabulku dokumentů, projekci objektu s textem rozložení a projekcí souboru.

Začněte přidáním nové Shaper dovednosti do pole dovednosti, které vytvoří objekt ve tvaru. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Výčnělky objektů vyžadují název kontejneru pro každou projekci, projekce objektů nebo projekce souborů nemohou sdílet kontejner. 

### <a name="relationships"></a>Relace

Tento příklad také zvýrazňuje jinou funkci projekce, protože definuje více typů projekce v rámci stejného objektu projekce, je vztah vyjádřen v rámci různých typů (tabulky, objekty, soubory) a mezi nimi, což umožňuje můžete začít s řádkem tabulky pro dokument a najít veškerý text optického rozpoznávání obrázků v tomto dokumentu v projekci objektu. Pokud nechcete, aby data byla v relaci, definujte projekce v různých objektech projekce, například následující fragment kódu bude mít za následek související tabulky, ale žádné relace mezi tabulkami a projekcemi textu v OCR. Skupiny projekce jsou užitečné v případě, že chcete projektovat stejná data v různých tvarech pro různé potřeby. Například skupina projekce pro řídicí panel Power BI a další skupinu projekce pro použití dat k vytvoření výukového modelu AI pro dovednost.
Při sestavování projekce různých typů jsou nejprve generovány soubory a projekce objektů a do tabulek jsou přidány cesty.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

Tyto příklady ukázaly běžné způsoby použití projekce, měli byste mít nyní také dobrý přehled o konceptech vytváření projekce pro konkrétní scénář.

## <a name="common-issues"></a>Běžné problémy

Při definování projekce je k dispozici několik běžných problémů, které mohou způsobit neočekávané výsledky.

1. Netvarování rozšíření řetězců. Když jsou řetězce obohaceny, například ```merged_content``` obohaceny klíčovými frázemi, je obohacená vlastnost reprezentována jako podřízená položka merged_content ve stromu rozšíření. Ale v době projekce je potřeba transformovat na platný objekt JSON s názvem a hodnotou.
2. Vynechává se ```/*``` na konci zdrojové cesty. Pokud je například zdroj projekce ```/document/pbiShape/keyPhrases``` pole klíčových frází je promítnuto jako jeden objekt nebo řádek. Nastavení zdrojové cesty pro ```/document/pbiShape/keyPhrases/*``` pro každou klíčovou frázi vytvoří jeden řádek nebo objekt.
3. V selektorech cest se rozlišují velká a malá písmena a můžou vést k chybějícím upozorněním vstupu, pokud nepoužíváte přesný případ pro selektor.

