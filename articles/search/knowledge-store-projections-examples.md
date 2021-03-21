---
title: Definování projekce ve znalostní bázi Knowledge Store
titleSuffix: Azure Cognitive Search
description: Příklady běžných vzorů o tom, jak promítnout obohacené dokumenty do úložiště znalostí pro použití s Power BI nebo Azure ML.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97509916"
---
# <a name="how-to-shape-and-export-enrichments"></a>Jak natvarovat a exportovat obohacení

Projekce představují fyzický výraz obohacených dokumentů ve znalostní bázi Knowledge Store. Efektivní používání obohacených dokumentů vyžaduje strukturu. V tomto článku prozkoumáte strukturu i vztahy, naučíte se, jak vytvořit vlastnosti projekce, a jak propojit data mezi vytvořenými typy projekce. 

Chcete-li vytvořit projekci, data jsou naformovaná pomocí [Shaper dovednosti](cognitive-search-skill-shaper.md) pro vytvoření vlastního objektu nebo použití syntaxe vloženého tvaru v rámci definice projekce. 

Datový tvar obsahuje všechna data, která jsou určena pro projekt, vytvořená jako hierarchie uzlů. Tento článek ukazuje několik postupů pro tvarování dat, aby je bylo možné promítnout do fyzických struktur, které mají za to, že se vytvářejí sestavy, analýzy nebo zpracování pro příjem dat. 

Příklady prezentované v tomto článku najdete v této [ukázce REST API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), kterou si můžete stáhnout a spustit v klientovi HTTP.

## <a name="introduction-to-projection-examples"></a>Příklady úvodu do projekce

Existují tři typy [projekce](knowledge-store-projection-overview.md):

+ Tabulky
+ Objekty
+ Soubory

Projekce tabulek jsou uloženy v úložišti tabulek Azure. Do úložiště objektů BLOB se napíší projekce objektů a souborů, kde se jako soubory JSON ukládají projekce objektů a můžou obsahovat obsah ze zdrojového dokumentu a také všechny jejich výstupy nebo obohacení dovedností. Kanál pro obohacení může také extrahovat binární soubory jako obrázky, tyto binární soubory jsou probíhají jako projekce souborů. Když je binární objekt promítnut jako projekce objektu, uloží se pouze metadata, která jsou k němu přidružená, jako objekt BLOB JSON. 

Abychom pochopili průnik mezi vytvářením a projekcí dat, použijeme jako základ pro prozkoumávání různých konfigurací následující dovednosti. Tato dovednosti zpracovává nezpracovaný obsah obrázku a textu. Projekce budou definované z obsahu dokumentu a výstupy dovedností, a to v případě požadovaných scénářů.

> [!IMPORTANT] 
> Při experimentování s projekcemi je užitečné [nastavit vlastnost mezipaměti indexeru](search-howto-incremental-index.md) , aby se zajistilo řízení nákladů. Pokud mezipaměť indexeru není nastavená, bude se po úpravách projekce znovu obohacen celý dokument. Když je mezipaměť nastavená a jenom aktualizované projekce, dovednosti spuštění pro dříve obohacené dokumenty nevede k žádným novým poplatkům za Cognitive Services.

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

Pomocí této dovednosti s hodnotou null `knowledgeStore` jako základu náš první příklad vyplní `knowledgeStore` výčnělky, které vytvoří tabulkové datové struktury, které můžeme použít v jiných scénářích. 

## <a name="projecting-to-tables"></a>Projekce do tabulek

Sestavování do tabulek v Azure Storage je užitečné pro vytváření sestav a analýzu pomocí nástrojů jako Power BI. Power BI mohou číst z tabulek a zjišťovat relace na základě klíčů, které jsou generovány během projekce. Pokud se pokoušíte vytvořit řídicí panel, zjednoduší se tím tato úloha. 

Pojďme vytvořit řídicí panel, který vizualizuje klíčové fráze extrahované z dokumentů jako Cloud Wordu. Chcete-li vytvořit správnou datovou strukturu, přidejte do dovednosti dovednost Shaper a vytvořte vlastní obrazec, který obsahuje podrobnosti a klíčové fráze specifické pro dokument. Vlastní tvar bude volán `pbiShape` v `document` kořenovém uzlu.

> [!NOTE] 
> Výčnělky tabulek jsou Azure Storage tabulky, které se řídí limity úložiště, které jsou uložené Azure Storage. Další informace najdete v tématu [omezení úložiště tabulek](/rest/api/storageservices/understanding-the-table-service-data-model). Je užitečné znát, že velikost entity nemůže být větší než 1 MB a jedna vlastnost nemůže být větší než 64 KB. Tato omezení vytvářejí v tabulkách dobré řešení pro ukládání velkého počtu malých entit.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Použití dovedností Shaper k vytvoření vlastního obrazce

Vytvořte vlastní tvar, který můžete promítnout do tabulkového úložiště. Bez vlastního tvaru může projekce odkazovat pouze na jeden uzel (jedna projekce na výstup). Vytvoření vlastního obrazce agreguje různé prvky do nového logického celku, který se může promítnout jako jedna tabulka nebo rozdělit a rozdělit do kolekce tabulek. 

V tomto příkladu vlastní obrazec kombinuje metadata a identifikované entity a klíčové fráze. Objekt se nazývá `pbiShape` a je v něm nadřazený `/document` . 

> [!IMPORTANT] 
> Jedním z účelů tvarování je zajistit, aby všechny uzly pro obohacení byly vyjádřené ve správném formátu JSON, který je potřeba pro projektování do znalostní báze Store. To platí hlavně v případě, že strom rozšíření obsahuje uzly, které nejsou ve správném formátu JSON (například když je obohacení nadřízený na primitivní jako řetězec).
>
> Všimněte si posledních dvou uzlů `KeyPhrases` a `Entities` . Ty jsou zabaleny do platného objektu JSON s `sourceContext` . To je nutné, protože `keyphrases` a `entities` jsou obohaceny na primitivních elementech a je nutné je převést na platný formát JSON předtím, než bude možné je promítnout.
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

Přidejte výše uvedenou Shaper dovednost do dovednosti. 

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

Teď, když máme všechna data potřebná pro projekt k tabulkám, aktualizujte objekt knowledgeStore o definice tabulky. V tomto příkladu máme tři tabulky definované nastavením `tableName` `source` `generatedKeyName` vlastností a.

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

Práci můžete zpracovat pomocí následujících kroků:

1. Nastavte ```storageConnectionString``` vlastnost na platný připojovací řetězec účtu úložiště pro obecné účely v2.  

1. Aktualizujte dovednosti vyvoláním žádosti PUT.

1. Po aktualizaci dovednosti spusťte indexer. 

Nyní máte pracovní projekci se třemi tabulkami. Import těchto tabulek do Power BI by měl mít za následek Power BI automatické zjišťování vztahů.

Než přejdete k dalšímu příkladu, pojďme přesměrovat aspekty projekce tabulky, abychom pochopili mechanismy vytváření řezů a související data.

### <a name="slicing"></a>Dělení 

Vytváření řezů je technika, která rozděluje celý sloučený obrazec na části prvků. Výsledek se skládá z oddělených, ale souvisejících tabulek, s kterými můžete pracovat samostatně.

V příkladu `pbiShape` je sloučený obrazec (nebo uzel obohacení). V definici projekce je rozdělena `pbiShape` do dalších tabulek, což umožňuje vytáhnout části tvaru ```keyPhrases``` a ```Entities``` . V Power BI to je užitečné, protože k jednotlivým dokumentům jsou přidruženy různé entity a klíčová fráze a získáte další přehledy, pokud můžete zobrazit entity a klíčová fráze jako data v kategoriích.

Vytváření řezů implicitně vygeneruje vztah mezi nadřazenými a podřízenými tabulkami pomocí ```generatedKeyName``` v nadřazené tabulce pro vytvoření sloupce se stejným názvem v podřízené tabulce. 

### <a name="naming-relationships"></a>Pojmenovávání vztahů

```generatedKeyName```Vlastnosti a ```referenceKeyName``` se používají k propojení dat napříč tabulkami nebo dokonce napříč typy projekce. Každý řádek v podřízené tabulce nebo projekci má vlastnost ukazující zpátky na nadřazenou položku. Název sloupce nebo vlastnosti v podřízeném ```referenceKeyName``` objektu je z nadřazené položky. Když ```referenceKeyName``` není zadaný, služba ho nastaví jako výchozí ```generatedKeyName``` z nadřazené. 

Power BI spoléhá na tyto generované klíče a zjistí vztahy v tabulkách. Pokud potřebujete sloupec v podřízené tabulce s názvem jinak, nastavte ```referenceKeyName``` vlastnost v nadřazené tabulce. Jedním z příkladů je nastavit ```generatedKeyName``` ID as v tabulce pbiDocument a ```referenceKeyName``` jako DocumentID. Výsledkem by byl sloupec v tabulkách pbiEntities a pbiKeyPhrases obsahující ID dokumentu s názvem DocumentID.

## <a name="projecting-to-objects"></a>Projekce do objektů

Výčnělky objektů nemají stejná omezení jako projekce tabulek a jsou vhodnější pro projekci rozsáhlých dokumentů. V tomto příkladu je celý dokument odeslán jako projekce objektu. Výčnělky objektů jsou omezeny na jednu projekci v kontejneru a nelze je rozdělit.

Pro definování projekce objektu použijte ```objects``` pole v projekce. Nový tvar můžete vygenerovat pomocí dovednosti v Shaper nebo pomocí vloženého tvaru projekce objektu. Přestože příklad tabulky ukázal přístup k vytvoření tvaru a vytváření řezů, tento příklad ukazuje použití vloženého tvaru. 

Vložené tvarování je schopnost vytvořit nový tvar v definici vstupů do projekce. Při vložené tvarování se vytvoří anonymní objekt, který je totožný s tím, co by Shaper dovednost vytvořila (v našem případě `pbiShape` ). Vložení tvarování je užitečné, pokud definujete tvar, který nehodláte znovu použít.

Vlastnost projekce je pole. Tento příklad přidá novou instanci projekce do pole, kde definice knowledgeStore obsahuje vložené projekce. Při použití vložených projekce můžete Shaper dovednosti vynechat.

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

## <a name="projecting-to-file"></a>Projektování do souboru

Výčnělky souborů jsou obrázky, které jsou extrahovány ze zdrojového dokumentu nebo výstupů obohacení, které mohou být vycházet z procesu rozšíření. Projekce souborů, podobně jako projekce objektů, jsou implementovány jako objekty BLOB v Azure Storage a obsahují obrázek. 

Chcete-li vygenerovat projekci souboru, použijte `files` pole v objektu projekce. Tento příklad projekty všechny obrázky extrahované z dokumentu do kontejneru s názvem `samplefile` .

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

Složitější scénář může vyžadovat, abyste promítáte obsah napříč typy projekce. Například pokud potřebujete projektovat některá data jako klíčové fráze a entity do tabulek, uložte výsledky optického rozpoznávání textu a textu rozložení jako objekty a potom obrázky naprojektujte jako soubory. 

Tento příklad aktualizuje dovednosti s následujícími změnami:

1. Vytvoří tabulku, která má řádek pro každý dokument.
1. Vytvořte tabulku související s tabulkou dokumentu s každou klíčovou frází identifikovanou jako řádek v této tabulce.
1. Vytvořte tabulku související s tabulkou dokumentu s každou entitou identifikovanou jako řádek v této tabulce.
1. Vytvořte projekci objektu s textem rozložení pro jednotlivé obrázky.
1. Vytvořte projekci souboru a promítáte každý extrahovaný obrázek.
1. Vytvořte tabulku s křížkem odkazem, která obsahuje odkazy na tabulku dokumentů, projekci objektu s textem rozložení a projekcí souboru.

Tyto změny se projeví v definici knowledgeStore. 

### <a name="shape-data-for-cross-projection"></a>Data obrazce pro křížovou projekci

Pro získání tvarů potřebných pro tyto projekce Začněte přidáním nové Shaper dovednosti, která vytvoří objekt ve tvaru s názvem `crossProjection` . 

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

### <a name="define-table-object-and-file-projections"></a>Definování projekce tabulek, objektů a souborů

Z konsolidovaného objektu crossProjection vyrovnejte objekt do několika tabulek, Zachyťte výstup optického rozpoznávání znaků jako objekty BLOB a uložte obrázek jako soubory (také ve službě BLOB Storage).

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

### <a name="relationships-among-table-object-and-file-projections"></a>Vztahy mezi projekcemi tabulek, objektů a souborů

Tento příklad také zvýrazní jinou funkci projekce. Definováním více typů projekce v rámci stejného objektu projekce je vztah vyjádřený v rámci různých typů (tabulky, objekty, soubory) a napříč nimi. To vám umožní začít s řádkem tabulky pro dokument a najít veškerý text optického rozpoznávání obrázků v tomto dokumentu v projekci objektu. 

Pokud nechcete, aby data byla v relaci, definujte projekce v různých objektech projekce. Například následující fragment kódu bude mít za následek související tabulky, ale bez relací mezi tabulkami a objekty (text OCR) projekce. 

Skupiny projekce jsou užitečné v případě, že chcete projektovat stejná data v různých tvarech pro různé potřeby. Například skupina projekce pro řídicí panel Power BI a další skupinu projekce pro zachycení dat, která se používají ke školení modelu Machine Learning zabaleného ve vlastní dovednosti.

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

## <a name="common-issues"></a>Běžné problémy

Při definování projekce je k dispozici několik běžných problémů, které mohou způsobit neočekávané výsledky. Pokud výstup ve znalostní bázi Store neočekáváte, vyhledejte tyto problémy.

+ Netvarování obohacení řetězců do platného formátu JSON. Když jsou řetězce obohaceny, například `merged_content` obohaceny klíčovými frázemi, je obohacená vlastnost vyjádřena jako podřízená položka `merged_content` ve stromu obohacení. Výchozí reprezentace není ve správném formátu JSON. Takže v době projekce nezapomeňte tuto obohacení transformovat na platný objekt JSON s názvem a hodnotou.

+ Vynechává se na ```/*``` konci zdrojové cesty. Pokud je zdrojem projekce `/document/pbiShape/keyPhrases` , pole klíčové fráze je promítnuto jako jeden objekt nebo řádek. Místo toho nastavte zdrojovou cestu tak, aby pro `/document/pbiShape/keyPhrases/*` každou klíčovou frázi vydávala jeden řádek nebo objekt.

+ Chyby syntaxe cesty V selektorech cest se rozlišují velká a malá písmena a můžou vést k chybějícím upozorněním vstupu, pokud nepoužíváte přesný případ pro selektor.

## <a name="next-steps"></a>Další kroky

Příklady v tomto článku ukazují běžné vzory způsobu vytváření projekce. Teď, když máte dobré znalosti o konceptech, je lepší zajistit vytváření projekce pro konkrétní scénář.

Při zkoumání nových funkcí zvažte možnost přírůstkové obohacení v dalším kroku. Přírůstkové obohacení je založeno na ukládání do mezipaměti, které umožňuje znovu použít jakékoli obohacení, které není jinak ovlivněné dovednosti úpravou. To je zvlášť užitečné pro kanály, které zahrnují analýzu OCR a obrázků.

> [!div class="nextstepaction"]
> [Úvod k přírůstkové obohacení a ukládání do mezipaměti](cognitive-search-incremental-indexing-conceptual.md)

Přehled o projekcích najdete v tématu Další informace o možnostech, jako jsou skupiny a vytváření řezů, a jak je [definujete v dovednosti](knowledge-store-projection-overview.md) .

> [!div class="nextstepaction"]
> [Projekce ve znalostní bázi Knowledge Store](knowledge-store-projection-overview.md)