---
title: Vnímání znalostí analýzy obrázků
titleSuffix: Azure Cognitive Search
description: Extrahujte sémantický text pomocí analýzy obrázků s využitím vnímání analýzy obrázků v kanálu rozšíření AI v Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 69b84a3edb606ed99b6aaca7db5ad0e57124f1b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948931"
---
# <a name="image-analysis-cognitive-skill"></a>Vnímání znalostí analýzy obrázků

Dovednost **analýzy obrázků** extrahuje bohatou sadu vizuálních funkcí založenou na obsahu obrázku. Můžete například vygenerovat titulek z obrázku, generovat značky nebo identifikovat celebrit a orientačních bodů. Tato dovednost používá v Cognitive Services modely strojového učení, které poskytuje [počítačové zpracování obrazu](../cognitive-services/computer-vision/overview.md) . 

> [!NOTE]
> Malé objemy (pod 20 transakcí) se dají v Azure Kognitivní hledání zdarma spustit, ale větší úlohy vyžadují [připojení fakturovatelné Cognitive Services prostředků](cognitive-search-attach-cognitive-services.md). Poplatky se účtují při volání rozhraní API v Cognitive Services a pro extrakci obrázků jako součást fáze pro vystavování dokumentů ve službě Azure Kognitivní hledání. Pro extrakci textu z dokumentů se neúčtují žádné poplatky.
>
> Při provádění integrovaných dovedností se účtují poplatky za stávající [Cognitive Services průběžných plateb](https://azure.microsoft.com/pricing/details/cognitive-services/). Ceny za extrakci imagí jsou popsané na [stránce s cenami za Azure kognitivní hledání](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. Vision. ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena.

| Název parametru     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |  Řetězec označující jazyk, který se má vrátit. Služba vrátí výsledky rozpoznávání v zadaném jazyce. Není-li tento parametr zadán, je použita výchozí hodnota "en". <br/><br/>Podporované jazyky: <br/>*EN* -angličtina (výchozí) <br/> *ES* – španělština <br/>  Japonsko – japonština <br/> *PT* – portugalština <br/> *zh* – zjednodušená čínština|
| `visualFeatures` |    Pole řetězců udávající typy vizuálních funkcí, které mají být vráceny. Mezi platné typy vizuálních funkcí patří:  <ul><li>*dospělý* – zjistí, jestli je obrázek pornografickýý (znázorňuje nahotu nebo sex), nebo je gorie (znázorňuje extrémní násilí nebo krev). Zjistila se také sexuální sugestivní obsah (označovaný také jako obsah pikantní).</li><li>*značky* – detekuje různé značky v rámci obrázku, včetně přibližného umístění. Funkce vizuálních *značek* je k dispozici pouze v angličtině.</li><li> *Categories* – roztřídí obsah obrázků podle taxonomie definované v [dokumentaci Cognitive Services počítačové zpracování obrazu](../cognitive-services/computer-vision/category-taxonomy.md). </li><li>*Popis* – popisuje obsah obrázku s úplnou větou v podporovaných jazycích.</li><li>*obličeje* – zjišťuje, zda jsou k dispozici plošky. Pokud je k dispozici, vygeneruje souřadnice, pohlaví a stáří.</li><li>  *objekty* – detekuje různé objekty v rámci obrázku, včetně přibližného umístění. Funkce vizuálů *objektů* je k dispozici pouze v angličtině.</li><li> *Tags* – Taguje obrázek pomocí podrobného seznamu slov souvisejících s obsahem obrázku.</li></ul> Názvy vizuálních funkcí rozlišují velká a malá písmena. Všimněte si, že vizuální funkce *Color* a *ImageType* se už nepoužívají, ale k této funkci můžete přistupovat prostřednictvím [vlastní dovednosti](./cognitive-search-custom-skill-interface.md).|
| `details` | Pole řetězců udávající, které podrobnosti specifické pro doménu se mají vrátit. Mezi platné typy vizuálních funkcí patří: <ul><li>*celebrit* – identifikuje celebrit, pokud se v imagi zjistí.</li><li>*orientačních bodů* – identifikuje orientační části, pokud jsou v imagi zjištěné. </li></ul> |

## <a name="skill-inputs"></a>Vstupy dovedností

| Název vstupu      | Description                                          |
|---------------|------------------------------------------------------|
| `image`         | Komplexní typ. V současné době funguje pouze s polem "/Document/normalized_images" vytvořeným indexerem Azure Blob, pokud ```imageAction``` je nastavena na jinou hodnotu než ```none``` . Další informace najdete v [ukázce](#sample-output) .|



##  <a name="sample-skill-definition"></a>Ukázka definice dovednosti

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Vzorový index (jenom pro pole kategorie, popis, obličeje a značky)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Ukázka mapování polí výstupu (pro výše uvedený index)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>Variace při mapování polí výstupu (vnořené vlastnosti)

Můžete definovat mapování polí pro výstup na vlastnosti nižší úrovně, jako jsou pouze orientačních bodů nebo celebrit. V takovém případě se ujistěte, že schéma indexu obsahuje pole, které konkrétně obsahuje orientační.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Ukázkový vstup

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Ukázkový výstup

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Chybové případy
V následujících chybových případech nejsou extrahovány žádné prvky.

| Kód chyby | Description |
|------------|-------------|
| `NotSupportedLanguage` | Zadaný jazyk není podporován. |
| `InvalidImageUrl` | Adresa URL obrázku je chybně naformátovaná nebo není přístupná.|
| `InvalidImageFormat` | Vstupní data nejsou platným obrázkem. |
| `InvalidImageSize` | Vstupní obrázek je příliš velký. |
| `NotSupportedVisualFeature`  | Zadaný typ funkce není platný. |
| `NotSupportedImage` | Nepodporovaná image, například podřízená pornografie. |
| `InvalidDetails` | Nepodporovaný model specifický pro doménu. |

Pokud se zobrazí chyba, která je podobná `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"` , ověřte cestu. Celebrit i orientačních bodů jsou vlastnosti v rámci `detail` .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Viz také

+ [Integrované dovednosti](cognitive-search-predefined-skills.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvoření indexeru (REST)](/rest/api/searchservice/create-indexer)