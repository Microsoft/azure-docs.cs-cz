---
title: Definování projekcí v úložišti znalostí
titleSuffix: Azure Cognitive Search
description: Příklady běžných vzorů, jak promítat rozšířené dokumenty do úložiště znalostí pro použití s Power BI nebo Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943679"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Projekce úložiště znalostí: Jak utvářet a vyvážet obohacení

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

Projekce jsou fyzickým vyjádřením obohacených dokumentů v úložišti znalostí. Efektivní využití vašich obohacených dokumentů vyžaduje strukturu. V tomto článku se podíváte na strukturu i vztahy, naučíte se, jak vytvářet vlastnosti projekce a jak propojit data mezi typy projekce, které vytvoříte. 

Chcete-li vytvořit projekci, musíte data tvarovat pomocí [dovednosti Shaper,](cognitive-search-skill-shaper.md) abyste vytvořili vlastní objekt, nebo použít syntaxi inline tvarování v definici projekce. 

Obrazec dat obsahuje všechna data, která chcete promítnout, vytvořená jako hierarchie uzlů. Tento článek ukazuje několik technik pro tvarování dat tak, aby je lze promítnout do fyzické struktury, které jsou příznivé pro vytváření sestav, analýzy nebo následné zpracování. 

Příklady uvedené v tomto článku naleznete v této [ukázce rozhraní REST API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), kterou můžete stáhnout a spustit v klientovi HTTP.

## <a name="introduction-to-the-examples"></a>Úvod k příkladům

Pokud jste obeznámeni s [projekcemi](knowledge-store-projection-overview.md), vzpomenete si, že existují tři typy:

+ Tabulky
+ Objekty
+ Soubory

Projekce tabulek se ukládají v úložišti Azure Table. Objekt a projekce souborů jsou zapsány do úložiště objektů blob, kde jsou projekce objektů uloženy jako soubory JSON a mohou obsahovat obsah ze zdrojového dokumentu a také všechny výstupy dovedností nebo obohacení. Kanál obohacení může také extrahovat binární soubory, jako jsou obrazy, tyto binární soubory jsou promítány jako projekce souborů. Když je binární objekt promítán jako projekce objektu, jsou jako objekt blob JSON uložena pouze metadata s ním spojená. 

Abychom porozuměli průsečíku mezi tvarováním dat a projekcemi, použijeme jako základ pro zkoumání různých konfigurací následující sadu dovedností. Tato skillset zpracovává nezpracovaný obsah obrazu a textu. Projekce budou definovány z obsahu dokumentu a výstupů dovedností pro scénáře, které chceme podpořit.

> [!IMPORTANT] 
> Při experimentování s projekcemi je užitečné [nastavit vlastnost mezipaměti indexeru,](search-howto-incremental-index.md) aby byla zajištěna kontrola nákladů. Úpravy projekcí bude mít za následek celý dokument je znovu obohacen, pokud indexer cache není nastavena. Pokud je mezipaměť nastavena a aktualizovány pouze projekce, provádění sady dovedností pro dříve obohacené dokumenty nevedou k žádným novým poplatkům služeb Cognitive Services.

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

Pomocí této skillset, `knowledgeStore` s jeho null jako základ, `knowledgeStore` náš první příklad vyplní objekt, nakonfigurované s projekcemi, které vytvářejí tabulkové datové struktury, které můžeme použít v jiných scénářích. 

## <a name="projecting-to-tables"></a>Promítání do tabulek

Promítání do tabulek ve Službě Azure Storage je užitečné pro vytváření sestav a analýzy pomocí nástrojů, jako je Power BI. Power BI umí číst z tabulek a zjišťovat relace založené na klíčích, které jsou generovány během projekce. Pokud se pokoušíte vytvořit řídicí panel, související data tento úkol zjednoduší. 

Předpokládejme, že se snažíme vytvořit řídicí panel, kde můžeme vizualizovat klíčové fráze extrahované z dokumentů jako slovní cloud. Chcete-li vytvořit správnou datovou strukturu, můžeme do sady dovedností přidat dovednost Shaper a vytvořit vlastní tvar, který obsahuje podrobnosti specifické pro dokument a klíčové fráze. Vlastní obrazec bude `pbiShape` volán v kořenovém `document` uzlu.

> [!NOTE] 
> Projekce tabulek jsou tabulky Azure Storage, které se řídí limity úložiště uloženými službou Azure Storage. Další informace naleznete v [tématu limity úložiště tabulky](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). Je užitečné vědět, že velikost entity nesmí překročit 1 MB a jedna vlastnost nemůže být větší než 64 kB. Tato omezení, aby tabulky dobrým řešením pro ukládání velkého počtu malých entit.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Vytvoření vlastního obrazce pomocí dovednosti Shaper

Vytvořte vlastní obrazec, který můžete promítnout do úložiště tabulek. Bez vlastního tvaru může projekce odkazovat pouze na jeden uzel (jeden projekci na výstup). Vytvoření vlastního obrazce umožňuje agregovat různé prvky do nového logického celku, který lze promítnout jako jednu tabulku nebo rozdělit na řezy a distribuovat mezi kolekci tabulek. 

V tomto příkladu vlastní obrazec kombinuje metadata a identifikované entity a klíčové fráze. Objekt je `pbiShape` volána a `/document`je nadřazený pod . 

> [!IMPORTANT] 
> Jedním z účelů tvarování je zajistit, aby všechny uzly obohacení byly vyjádřeny v dobře tvarovaném JSON, který je nutný pro promítání do úložiště znalostí. To platí zejména v případě, že strom obohacení obsahuje uzly, které nejsou ve správném formátu JSON (například když obohacení je nadřazený primitivní jako řetězec).
>
> Všimněte si posledních `KeyPhrases` dvou `Entities`uzlů a . Ty jsou zabaleny do platného objektu `sourceContext`JSON s . To je `keyphrases` vyžadováno jako a `entities` jsou obohacení na primitiva a je třeba převést na platné JSON před jejich promítat.
>


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

Přidejte výše uvedenou dovednost Shaper do sady dovedností. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Nyní, když máme všechna data potřebná k promítání do tabulek, aktualizujte objekt knowledgeStore s definicemi tabulek. V tomto příkladu máme tři tabulky, `tableName` `source` definované `generatedKeyName` nastavením , a vlastnosti.

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

Svou práci můžete zpracovat následujícím postupem:

1. Nastavte ```storageConnectionString``` vlastnost na platný připojovací řetězec účtu úložiště Pro obecné účely V2.  

1. Aktualizujte sadu dovedností vydáním požadavku PUT.

1. Po aktualizaci sady dovedností spusťte indexer. 

Nyní máte pracovní projekci se třemi tabulkami. Import těchto tabulek do Power BI by měl vést k automatickému zjišťování relací Power BI.

Před přechodem na další příklad umožňuje znovu aspekty projekce tabulky pochopit mechaniku krájení a související data.

### <a name="slicing"></a>Krájení 

Krájení je technika, která rozděluje celý konsolidovaný tvar na jednotlivé části. Výsledek se skládá ze samostatných, ale souvisejících tabulek, se kterými můžete pracovat jednotlivě.

V příkladu `pbiShape` je konsolidovaný tvar (nebo uzel obohacení). V definici `pbiShape` projekce je rozdělendo dalších tabulek, což umožňuje ```keyPhrases``` vytáhnout ```Entities```části tvaru a . V Power BI je to užitečné, protože ke každému dokumentu je přidruženo více entit a klíčových frází a získáte další přehledy, pokud vidíte entity a klíčové fráze jako kategorizovaná data.

Krájení implicitně generuje relaci mezi nadřazenou ```generatedKeyName``` a podřízenou tabulkou a pomocí tabulky v nadřazené tabulce vytvoří sloupec se stejným názvem v podřízené tabulce. 

### <a name="naming-relationships"></a>Pojmenování vztahů

```generatedKeyName``` Vlastnosti ```referenceKeyName``` a se používají k propojení dat mezi tabulkami nebo dokonce mezi typy projekce. Každý řádek v podřízené tabulce/projekci má vlastnost směřující zpět na nadřazenou položku. Název sloupce nebo vlastnosti v podřízeném objektu ```referenceKeyName``` je od nadřazeného. Pokud ```referenceKeyName``` není k dispozici, služba výchozí od ```generatedKeyName``` nadřazeného. 

Power BI spoléhá na tyto generované klíče ke zjišťování relací v tabulkách. Pokud potřebujete sloupec v podřízené tabulce s ```referenceKeyName``` názvem jinak, nastavte vlastnost v nadřazené tabulce. Jedním z příkladů by ```generatedKeyName``` bylo nastavit jako ID v ```referenceKeyName``` tabulce pbiDocument a jako DocumentID. Výsledkem by byl sloupec v tabulkách pbiEntities a pbiKeyPhrases obsahující id dokumentu s názvem DocumentID.

## <a name="projecting-to-objects"></a>Promítání na objekty

Objektové projekce nemají stejná omezení jako projekce tabulky a jsou vhodnější pro promítání velkých dokumentů. V tomto příkladu promítáme celý dokument do projekce objektu. Projekce objektů jsou omezeny na jednu projekci v kontejneru a nelze je rozdělit na řezy.

Chcete-li definovat projekci ```objects``` objektu, použijeme pole v projekcích. Nový tvar můžete vygenerovat pomocí dovednosti Shaper nebo použít vřádkové tvarování projekce objektu. Zatímco příklad tabulky demonstroval přístup k vytvoření tvaru a krájení, tento příklad ukazuje použití inline tvarování. 

Vložené tvarování je schopnost vytvořit nový tvar v definici vstupů pro projekci. Inline tvarování vytvoří anonymní objekt, který je totožný s `pbiShape`tím, co shaper dovednost by vyrábět (v našem případě, ). Vřádkové tvarování je užitečné, pokud definujete obrazec, který nechcete znovu použít.

Vlastnost projekce je pole. V tomto příkladu přidáváme novou instanci projekce do pole, kde definice knowledgeStore obsahuje vslané projekce. Při použití vřádkových projekcí můžete vynechat dovednost Shaper.

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

## <a name="projecting-to-file"></a>Promítání do souboru

Projekce souborů jsou obrazy, které jsou extrahovány ze zdrojového dokumentu nebo výstupy obohacení, které lze promítnout z procesu obohacení. Projekce souborů, podobně jako objektové projekce, se implementují jako objekty BLOB ve službě Azure Storage a obsahují bitovou kopii. 

Pro generování projekce souboru `files` použijeme pole v objektu projekce. Tento příklad projekty všechny obrázky extrahované z dokumentu do kontejneru s názvem `samplefile`.

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

## <a name="projecting-to-multiple-types"></a>Promítání na více typů

Složitější scénář může vyžadovat promítat obsah napříč typy projekce. Pokud například potřebujete promítnout některá data, jako jsou klíčové fráze a entity, uložte výsledky rozpoznávání OCR textu a textu rozložení jako objekty a potom promítají obrazy jako soubory. 

V tomto příkladu aktualizace sady dovedností zahrnují následující změny:

1. Vytvořte tabulku s řádkem pro každý dokument.
1. Vytvořte tabulku související s tabulkou dokumentů s každou klíčovou frází označenou jako řádek v této tabulce.
1. Vytvořte tabulku související s tabulkou dokumentů s každou entitou označenou jako řádek v této tabulce.
1. Vytvořte objektovou projekci s textem rozvržení pro každý obraz.
1. Vytvořte projekci souborů a promítnete každý extrahovaný obraz.
1. Vytvořte tabulku křížových odkazů, která obsahuje odkazy na tabulku dokumentu, projekci objektů s textem rozvržení a projekcí souboru.

Tyto změny se projeví v definici knowledgeStore dále dolů. 

### <a name="shape-data-for-cross-projection"></a>Data obrazce pro křížovou projekci

Chcete-li získat tvary, které potřebujeme pro tyto projekce, začněte přidáním nové dovednosti Shaper, která vytvoří tvarovaný objekt s názvem `crossProjection`. 

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

### <a name="define-table-object-and-file-projections"></a>Definování projekcí tabulek, objektů a souborů

Z konsolidovaného objektu crossProjection můžeme objekt rozdělit do více tabulek, zachytit výstup OCR jako objekty BLOB a potom uložit obraz jako soubory (také v úložišti objektů Blob).

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

Objektové projekce vyžadují název kontejneru pro každou projekci, objektové projekce nebo projekce souborů nemohou sdílet kontejner. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relace mezi projekcemi tabulek, objektů a souborů

Tento příklad také zvýrazní další prvek projekcí. Definováním více typů projekcí v rámci stejného objektu projekce existuje relace vyjádřená v rámci různých typů a mezi ně (tabulky, objekty, soubory), což umožňuje začít s řádkem tabulky pro dokument a najít veškerý text Rozpoznávání OCR pro obrazy v rámci tohoto dokumentu v projekci objektu. 

Pokud nechcete, aby se data týkala, definujte projekce v různých projekčních objektech. Například následující úryvek bude mít za následek tabulky související, ale bez relací mezi tabulkami a objektem (text ocr) projekce. 

Projekční skupiny jsou užitečné, pokud chcete promítat stejná data v různých tvarech pro různé potřeby. Například projekční skupina pro řídicí panel Power BI a další projekční skupina pro sběr dat používaná k trénování modelu strojového učení zabalené ho do vlastní dovednosti.

Při vytváření projekcí různých typů jsou nejprve generovány projekce souborů a objektů a cesty jsou přidány do tabulek.

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

## <a name="common-issues"></a>Běžné problémy

Při definování projekce existuje několik běžných problémů, které mohou způsobit neočekávané výsledky. Zkontrolujte tyto problémy, pokud výstup v úložišti znalostí není co očekáváte.

+ Netvarování obohacování řetězců do platného JSON. Když řetězce jsou obohaceny, `merged_content` například obohacené klíčové fráze, obohacené vlastnost `merged_content` je reprezentován jako podřízený v rámci stromu obohacení. Výchozí reprezentace není ve správném formátu JSON. Takže v době projekce, ujistěte se, že transformovat obohacení do platného objektu JSON s názvem a hodnotou.

+ Vynechání ```/*``` na konci zdrojové cesty. Pokud je `/document/pbiShape/keyPhrases`zdrojem projekce , pole klíčových frází je promítáno jako jeden objekt/řádek. Místo toho nastavte zdrojovou cestu tak, aby `/document/pbiShape/keyPhrases/*` přinesla jeden řádek nebo objekt pro každou z klíčových frází.

+ Chyby syntaxe cesty. Voliči cest rozlišují malá a velká písmena a mohou vést k chybějícím vstupním upozorněním, pokud nepoužíváte přesný případ pro voliče.

## <a name="next-steps"></a>Další kroky

Příklady v tomto článku ukazují běžné vzory o tom, jak vytvořit projekce. Nyní, když máte dobré znalosti o konceptech, jste lépe vybaveni k vytváření projekcí pro váš konkrétní scénář.

Při zkoumání nových funkcí zvažte přírůstkové obohacení jako další krok. Přírůstkové obohacení je založeno na ukládání do mezipaměti, které umožňuje znovu použít všechny obohacení, které nejsou jinak ovlivněny změnou sady dovedností. To je užitečné zejména pro kanály, které obsahují rozpoznávání OCR a analýzu obrázků.

> [!div class="nextstepaction"]
> [Úvod do postupného obohacování a ukládání do mezipaměti](cognitive-search-incremental-indexing-conceptual.md)

Přehled projekcí najdete další informace o možnostech, jako jsou skupiny a krájení, a o tom, jak [je definujete v sadě dovedností.](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projekce v obchodě se znalostmi](knowledge-store-projection-overview.md)

