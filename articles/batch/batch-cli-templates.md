---
title: Spouštění úloh od konce do konce pomocí šablon – Azure Batch
description: Pomocí pouze příkazů rozhraní příkazového příkazu můžete vytvořit fond, nahrát vstupní data, vytvořit úlohy a přidružené úkoly a stáhnout výsledná výstupní data.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c7459c4dc700f034feafbf133b831a52b9233d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020161"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Použití šablon Nastavení uživatelského příkazu Azure batch a přenosu souborů

Pomocí rozšíření Azure Batch pro Azure CLI, je možné spustit dávkové úlohy bez psaní kódu.

Vytvořte a použijte soubory šablon JSON pomocí azure CLI k vytvoření fondů dávek, úloh a úloh. Pomocí příkazů rozšíření rozhraní PŘÍKAZOVÉHO PŘÍKAZU můžete snadno nahrát vstupní soubory úloh do účtu úložiště přidruženého k účtu Batch a stáhnout výstupní soubory úloh.

## <a name="overview"></a>Přehled

Rozšíření azure cli umožňuje batch používat end-to-end uživatelé, kteří nejsou vývojáři. Pomocí pouze příkazů rozhraní příkazového příkazu můžete vytvořit fond, nahrát vstupní data, vytvořit úlohy a přidružené úkoly a stáhnout výsledná výstupní data. Není vyžadován žádný další kód. Spusťte příkazy příkazového příkazu CLI přímo nebo je integrujte do skriptů.

Dávkové šablony se staví na [existující podpoře batch v azure cli](batch-cli-get-started.md#json-files-for-resource-creation) pro soubory JSON k určení hodnot vlastností při vytváření fondů, úloh, úkolů a dalších položek. Dávkové šablony přidávají následující funkce:

-   Parametry mohou být definovány. Při použití šablony jsou určeny pouze hodnoty parametrů pro vytvoření položky s dalšími hodnotami vlastností položky zadanými v textu šablony. Uživatel, který rozumí Batch a aplikacím, které mají být spuštěny společností Batch, může vytvářet šablony, které určují hodnoty vlastností fondu, úlohy a úlohy. Uživatel méně obeznámený s Batch a/nebo aplikace mi musí zadat pouze hodnoty pro definované parametry.

-   Továrny úloh vytvářejí jeden nebo více úkolů přidružených k projektu, čímž se zabrání nutnosti vytvářet mnoho definic úkolů a výrazně zjednoduší odesílání úloh.


Úlohy obvykle používají vstupní datové soubory a vytvářejí výstupní datové soubory. Účet úložiště je ve výchozím nastavení přidružen ke každému účtu Batch. Přenos souborů do a z tohoto účtu úložiště pomocí cli, bez kódování a bez pověření úložiště.

Například [ffmpeg](https://ffmpeg.org/) je populární aplikace, která zpracovává audio a video soubory. Tady jsou kroky s nastavením příkazu k příkazu Azure Batch pro vyvolání ffmpeg u zdrojových video souborů překódovat do různých rozlišení.

-   Vytvořte šablonu fondu. Uživatel, který šablonu vytváří, ví, jak volat aplikaci ffmpeg a její požadavky; určují odpovídající operační systém, velikost virtuálního počítače, způsob instalace ffmpegu (například z balíčku aplikace nebo pomocí správce balíčků) a další hodnoty vlastností fondu. Parametry jsou vytvořeny tak, že při použití šablony je třeba zadat pouze ID fondu a počet virtuálních počítače.

-   Vytvořte šablonu úlohy. Uživatel, který šablonu vytváří, ví, jak je třeba ffmpeg vyvolat k překódování zdrojového videa do jiného rozlišení, a určuje příkazový řádek úkolu. vědí také, že existuje složka obsahující zdrojové video soubory, s úlohou požadovanou pro vstupní soubor.

-   Koncový uživatel se sadou video souborů k překódování nejprve vytvoří fond pomocí šablony fondu a určí pouze ID fondu a počet požadovaných virtuálních počítače. Poté mohou nahrát zdrojové soubory do překódování. Úlohu lze poté odeslat pomocí šablony úlohy a zadat pouze ID fondu a umístění odeslaných zdrojových souborů. Dávková úloha je vytvořena s jedním úkolem na vstupní soubor, který je generován. Nakonec lze stáhnout překódované výstupní soubory.

## <a name="installation"></a>Instalace

Chcete-li nainstalovat rozšíření Azure Batch CLI, [nejprve nainstalujte Azure CLI 2.0](/cli/azure/install-azure-cli)nebo spusťte Azure CLI v [Azure Cloud Shell](../cloud-shell/overview.md).

Nainstalujte nejnovější verzi rozšíření Batch pomocí následujícího příkazu Azure CLI:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Další informace o rozšíření rozhraní ROZHRANÍ NEBO INSTALACI dávky a další možnosti instalace naleznete v [úložišti GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Chcete-li použít funkce rozšíření cli, potřebujete účet Azure Batch a pro příkazy, které přenášejí soubory do a z úložiště, propojený účet úložiště.

Pokud se chcete přihlásit k dávkovému účtu pomocí azure cli, přečtěte si informace [o správě dávkových prostředků pomocí azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Šablony

Šablony Azure Batch jsou podobné šablonám Azure Resource Manageru, ve funkčnosti a syntaxi. Jedná se o soubory JSON, které obsahují názvy vlastností položky a hodnoty, ale přidejte následující hlavní koncepty:

-   **Parametry**

    -   Povolit hodnoty vlastností, které mají být zadány v části textu, s pouze hodnoty parametrů, které je třeba zadat při použití šablony. Například úplná definice fondu může být umístěna v těle a pouze jeden parametr definovaný pro ID fondu; pouze řetězec ID fondu proto musí být dodány k vytvoření fondu.
        
    -   Tělo šablony může být vytvořen o někoho se znalostí Batch a aplikace, které mají být spuštěny Batch; při použití šablony musí být zadány pouze hodnoty pro parametry definované autorem. Uživatel bez podrobné dávky a/nebo znalosti aplikace proto můžete použít šablony.

-   **Proměnné**

    -   Povolit jednoduché nebo složité hodnoty parametrů, které mají být zadány na jednom místě a použity na jednom nebo více místech v těle šablony. Proměnné můžete zjednodušit a zmenšit velikost šablony, stejně jako aby bylo více udržovatelné tím, že má jedno umístění pro změnu vlastností.

-   **Konstrukce vyšší úrovně**

    -   Některé konstrukce vyšší úrovně jsou k dispozici v šabloně, které ještě nejsou k dispozici v dávkových rozhraních API. Například továrna úloh může být definována v šabloně úlohy, která vytváří více úkolů pro úlohu pomocí společné definice úlohy. Tyto konstrukce vyhnout nutnosti kódu dynamicky vytvářet více souborů JSON, jako je například jeden soubor na úlohu, stejně jako vytvořit soubory skriptů pro instalaci aplikací prostřednictvím správce balíčků.

    -   V určitém okamžiku tyto konstrukce mohou být přidány do dávkové služby a k dispozici v dávkových rozhraních API, rozhraní chio atd.

### <a name="pool-templates"></a>Šablony fondu

Šablony fondu podporují standardní možnosti šablony parametrů a proměnných. Podporují také následující konstrukce vyšší úrovně:

-   **Odkazy na balíčky**

    -   Volitelně umožňuje kopírování softwaru do uzlů fondu pomocí správců balíčků. Správce balíčků a ID balíčku jsou určeny. Deklarováním jednoho nebo více balíčků se vyhnete vytvoření skriptu, který získá požadované balíčky, instalaci skriptu a spuštění skriptu v každém uzlu fondu.

Následuje příklad šablony, která vytvoří fond virtuálních počítačích s Linuxem s nainstalovaným ffmpeg. Chcete-li jej použít, zařazujete pouze řetězec ID fondu a počet virtuálních her ve fondu:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Pokud byl soubor šablony pojmenován _pool-ffmpeg.json_, vyvolá tež.

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Rozhraní příkazového řádku vás vyzve `poolId` k `nodeCount` zadání hodnot pro parametry a. Můžete také zadat parametry v souboru JSON. Například:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Pokud parametry Soubor JSON byl pojmenován *pool-parameters.json*, potom vyvolat šablonu takto:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Šablony úloh

Šablony úloh podporují standardní možnosti šablony parametrů a proměnných. Podporují také následující konstrukce vyšší úrovně:

-   **Továrna úloh**

    -   Vytvoří více úkolů pro úlohu z jedné definice úkolu. Podporovány jsou tři typy vytváření úloh – parametrické tažení, úloha na soubor a kolekce úloh.

Následuje příklad šablony, která vytvoří úlohu pro překódování videosouborů MP4 s ffmpeg na jedno ze dvou nižších rozlišení. Vytvoří jeden úkol na zdrojový video soubor. Další informace o skupinách souborů pro vstup a výstup úlohy najdete v [tématu Skupiny souborů](#file-groups-and-file-transfer) a přenos souborů.

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Pokud byl soubor šablony pojmenován _job-ffmpeg.json_, vyvolá tež.

```azurecli
az batch job create --template job-ffmpeg.json
```

Stejně jako dříve, rozhraní příkazového řádku vás vyzve k zadání hodnot pro parametry. Můžete také zadat parametry v souboru JSON.

### <a name="use-templates-in-batch-explorer"></a>Použití šablon v Průzkumníkovi dávek

Šablonu rozhraní NEBO UŽIVATELSKÉrozhraní dávky můžete nahrát do desktopové aplikace [Batch Explorer](https://github.com/Azure/BatchExplorer) (dříve nazývané BatchLabs) a vytvořit tak fond nebo úlohu batch. Můžete také vybrat z předdefinovaného fondu a šablon úloh v Galerii dávkového průzkumníka.

Jak nahrát šablonu:

1. V Průzkumníku dávek vyberte**místní šablony** **galerie** > .

2. Vyberte nebo přetáhněte místní fond nebo šablonu úlohy.

3. Vyberte **Použít tuto šablonu**a postupujte podle pokynů na obrazovce.

## <a name="file-groups-and-file-transfer"></a>Skupiny souborů a přenos souborů

Většina úloh a úloh vyžaduje vstupní soubory a vytváří výstupní soubory. Vstupní soubory a výstupní soubory jsou obvykle přenášeny, buď z klienta do uzlu, nebo z uzlu do klienta. Rozšíření Azure Batch CLI abstrahuje přenos souborů pryč a využívá účet úložiště, který můžete přidružit ke každému účtu Batch.

Skupina souborů se rovná kontejneru, který se vytvoří v účtu úložiště Azure. Skupina souborů může mít podsložky.

Přípona rozhraní PŘÍKAZU dávky poskytuje příkazy pro nahrávání souborů z klienta do zadané skupiny souborů a stahování souborů ze zadané skupiny souborů do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Šablony fondu a úloh umožňují zadat soubory uložené ve skupinách souborů pro kopírování do uzlů fondu nebo mimo uzly fondu zpět do skupiny souborů. Například v šabloně úlohy zadané dříve je pro objekt pro vytváření úloh určen *ffmpeg-input* skupiny souborů jako umístění zdrojových video souborů zkopírovaných dolů do uzlu pro překódování. Skupina souborů *ffmpeg-output* je umístění, kde jsou překódované výstupní soubory zkopírovány z uzlu spuštěného každou úlohu.

## <a name="summary"></a>Souhrn

Podpora přenosu šablon a souborů byla aktuálně přidána jenom do příkazového příkazového příkazu k Řešení azure. Cílem je rozšířit okruh uživatelů, které můžete použít Batch pro uživatele, kteří nepotřebují vyvíjet kód pomocí dávkových api, jako jsou výzkumníci a uživatelé IT. Bez kódování mohou uživatelé se znalostmi Azure, Batch a aplikací, které mají být spuštěny společností Batch, vytvářet šablony pro vytváření fondu a úloh. S parametry šablony mohou uživatelé bez podrobné znalosti Batch a aplikací používat šablony.

Vyzkoušejte rozšíření Batch pro azure cli a poskytněte nám zpětnou vazbu nebo návrhy, a to buď v komentářích k tomuto článku, nebo prostřednictvím [repo komunity dávek](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Další kroky

- Podrobná dokumentace k instalaci a použití, ukázky a zdrojový kód jsou k dispozici v [úložišti Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).

- Přečtěte si další informace o vytváření a správě dávkových prostředků pomocí [Dávkového průzkumníka.](https://github.com/Azure/BatchExplorer)
