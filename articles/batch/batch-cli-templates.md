---
title: Spustit Azure Batch úlohy klient server bez nutnosti psaní kódu (Preview) | Microsoft Docs
description: Vytvoření šablony souborů pro rozhraní příkazového řádku Azure k vytvoření dávky fondy, úlohy a úkoly.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/18/2017
ms.author: markscu
ms.openlocfilehash: 4dd9218b982860e62e04b46fb5d07e5553407599
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130848"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Použití šablon rozhraní příkazového řádku služby Batch a přenos souborů (Preview)

Pomocí rozhraní příkazového řádku Azure je možné spustit úlohy Batch bez nutnosti psaní kódu.

Vytvořit a použít soubory šablon pomocí rozhraní příkazového řádku Azure k vytvoření dávky fondy, úlohy a úkoly. Snadno odeslání úlohy vstupní soubory k účtu úložiště přidružené k účtu Batch a stažení výstupní soubory.

## <a name="overview"></a>Přehled

Rozšíření pro rozhraní příkazového řádku Azure umožňuje Batch být použité kompletní uživatelé, kteří nejsou vývojáři. Jenom příkazy rozhraní příkazového řádku můžete vytvořit fond, nahrát vstupních dat, vytvoření úlohy a související úlohy a stáhnout výsledný výstupní data. Vyžaduje se žádný další kód. Přímo spustit příkazy rozhraní příkazového řádku nebo jejich integraci do skriptů.

Vytvoření šablony batch ve [existující Batch podpora v rozhraní příkazového řádku Azure](batch-cli-get-started.md#json-files-for-resource-creation) pro soubory JSON k určení hodnoty vlastností, při vytváření fondů, úloh, úlohy a další položky. S šablonami Batch jsou přidány následující možnosti přes co je možné pomocí soubory JSON:

-   Parametry lze definovat. Pokud se použije šablona, jsou pro vytvoření položky s jiné položky hodnoty vlastností specifikované v těle šablony zadat pouze hodnoty parametru. Uživatel, který jste srozuměni s tím Batch a aplikace, které se má spustit Batch můžete vytvořit šablony, fond, úlohy a hodnoty vlastností úlohy. Menší neznáte Batch nebo aplikace potřebuje uživatel jen zadat hodnoty pro parametry definované.

-   Objekty pro vytváření úloh úlohy vytvořit jeden nebo více úkoly spojené s úlohu, zabraňující potřebu mnoho definic úloh vytvoření a výrazně zjednodušit úlohy.


Úlohy obvykle použít vstupních datových souborů a vytvoření výstupní datové soubory. Ve výchozím nastavení se každého účtu Batch je přidružený účet úložiště. Přenos souborů do a z tohoto účtu úložiště pomocí rozhraní příkazového řádku s žádné kódování a žádné přihlašovací údaje úložiště.

Například [ffmpeg](http://ffmpeg.org/) je Oblíbené aplikace, která zpracovává soubory audia a videa. Tady jsou kroky, pomocí rozhraní příkazového řádku Azure Batch pro vyvolání ffmpeg převeďte zdroje videosoubory pro různá řešení.

-   Vytvořte šablonu fondu. Uživatel vytváření šablony umí volání aplikace ffmpeg a požadavky na jeho; určí příslušné operačního systému, počítač velikost, jak ffmpeg je nainstalovaná (z balíčku aplikace nebo pomocí Správce balíčků, například) a dalších fond hodnoty vlastností. Parametry jsou vytvořeny tak, že pokud se použije šablona, je potřeba zadat ID fondu a počet virtuálních počítačů.

-   Vytvoření šablony úlohy. Uživatel vytváření šablony vědět, jak se ffmpeg musí být volána převod zdroj videa na jiné rozlišení a určuje příkazový řádek úkolu; také věděli, že je složka obsahující zdrojové soubory videa, s úloha vyžaduje pro vstupní soubor.

-   Koncový uživatel se sadou videosouborů převod nejprve vytvoří fond pomocí šablony fondu, zadáte jenom ID fondu a počet virtuálních počítačů, které jsou potřeba. Převod se pak můžete nahrát zdrojové soubory. Úlohy lze odeslat pak pomocí šablony úlohy, zadáte jenom ID fondu a umístění zdrojových souborů nahrát. Jeden úkol za vstupní soubor generován se vytvoří dávkovou úlohu. Nakonec můžete stáhnout výstupní soubory převodu.

## <a name="installation"></a>Instalace

Nainstalujte rozšíření rozhraní příkazového řádku Azure Batch pomocí této šablony a souborů možnosti přenosu.

Pokyny o tom, jak nainstalovat rozhraní příkazového řádku Azure najdete v tématu [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli).

Po instalaci rozhraní příkazového řádku Azure, nainstalujte nejnovější verzi rozšíření Batch pomocí rozhraní příkazového řádku následující příkaz:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Další informace o rozšíření Batch najdete v tématu [Microsoft Azure Batch CLI rozšíření pro Windows, Mac a Linux](https://github.com/Azure/azure-batch-cli-extensions#microsoft-azure-batch-cli-extensions-for-windows-mac-and-linux).

## <a name="templates"></a>Šablony

Rozhraní příkazového řádku Azure Batch umožňuje položek, jako jsou fondy, úlohy a úkoly, které mají být vytvořeny tak, že zadáte soubor JSON obsahující názvy a hodnoty vlastností. Příklad:

```azurecli
az batch pool create –-json-file AppPool.json
```

Šablony Azure Batch jsou podobné šablony Azure Resource Manager, v syntaxi a funkce. Jsou soubory JSON, které obsahují položky názvy a hodnoty vlastností, ale přidat následující hlavní koncepty:

-   **Parametry**

    -   Povolit hodnoty vlastností pro zadat v části textu, pouze hodnoty parametrů museli zadat při použití šablony. Například dokončení definice pro fond může být umístěny v textu a jenom jeden parametr definovaný pro id fondu; proto musí být zadán vytvoření fondu pouze řetězec ID fondu.
        
    -   Šablona textu můžete vytvořené uživatelem s znalosti Batch a aplikace, které se má spustit dávky; Při použití šablony je nutné zadat pouze hodnoty pro parametry definované autora. Může uživatel bez podrobný Batch nebo znalostní bázi aplikace proto používat šablony.

-   **Proměnné**

    -   Povolit parametr jednoduché nebo komplexní hodnoty zadané v jednom místě a použít na jeden nebo více místech v těle šablony. Proměnné můžete zjednodušit a zmenšit velikost šablony, a také zkontrolujte více udržovatelný tak, že jedno umístění vlastnosti chcete změnit.

-   **Konstrukce vyšší úrovně**

    -   Některé konstrukce vyšší úrovně jsou k dispozici v šabloně, které ještě nejsou k dispozici v rozhraní API služby Batch. Například objekt pro vytváření úloh lze definovat v šabloně úlohy, která vytvoří více úloh pro úlohu pomocí společná definice úlohy. Tyto konstrukce nemuseli kód dynamicky vytvořte několik souborů JSON, jako je například jeden soubor pro úlohy, a také vytvořit skript soubory k instalaci aplikací prostřednictvím Správce balíčků.

    -   V určitém okamžiku může být tyto konstrukce přidané do služby Batch a k dispozici v rozhraní API služby Batch, uživatelská atd.

### <a name="pool-templates"></a>Šablony fondu

Šablony fondu podporují možnosti standardní šablona parametry a proměnné. Také podporují následující konstrukce vyšší úrovně:

-   **Odkazy na balíček**

    -   Umožňuje volitelně softwaru zkopírují na uzly fondu pomocí Správce balíčků. Správce balíčků a ID balíčku nejsou zadány. Deklarování jeden nebo více balíčků, vyhnete vytváření skript, který získá požadované balíčky, skript instalace a spuštění skriptu na všech uzlech fondu.

Následuje příklad šablony, která vytvoří fond virtuálních počítačů Linux s ffmpeg nainstalována. Pokud chcete použít, zadejte pouze ID řetězec fondu a počet virtuálních počítačů ve fondu:

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

Pokud se název souboru šablony _fondu ffmpeg.json_, pak vyvolání šablony následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Šablony úloh

Šablony úloh podporují možnosti standardní šablona parametry a proměnné. Také podporují následující konstrukce vyšší úrovně:

-   **Objekt pro vytváření úloh**

    -   Vytvoří více úkolů pro úlohu z definice jeden úkol. Tři typy objektu pro vytváření úloh jsou podporovány – čištění oblouku úloh na soubor a kolekce úloh.

Následuje příklad šablony, která vytvoří úlohu převod s videosoubory MP4 s ffmpeg na jednu ze dvou nižší rozlišení. Vytvoří jeden úkol na video zdrojového souboru:

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

Pokud se název souboru šablony _úlohy ffmpeg.json_, pak vyvolání šablony následujícím způsobem:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Skupiny souborů a přenos souborů

Většina úloh a úloh vyžadují vstupní soubory a vytvoří výstupní soubory. Obvykle soubory vstupní a výstupní soubory se přenášejí, od klienta k uzlu, nebo z uzlu do klienta. Rozšíření rozhraní příkazového řádku Azure Batch abstrahuje přenos nepřítomnosti souborů a využívá účet úložiště, který se vytvoří ve výchozím nastavení pro každý účet Batch.

Skupinu souborů rovná kontejner, který je vytvořen v účtu úložiště Azure. Skupina souborů může mít podsložky.

Rozšíření rozhraní příkazového řádku Batch poskytuje příkazy, které soubory z klienta do skupiny zadaný soubor nahrávání a stahování souborů ze skupiny zadaný soubor pro klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Šablony fondu a úlohy umožňují soubory uložené v souboru skupiny pro kopírování na uzly fondu nebo vypnout uzly fondu zpět do skupiny souborů. Například v šabloně projektů dříve zadaný soubor skupiny "ffmpeg – vstup" je zadán pro objekt pro vytváření úloh jako umístění zdrojových souborů video zkopírovat dolů na uzel pro překódování; soubor skupiny "ffmpeg-output" je umístění, kam převodu výstupní soubory jsou zkopírovány z uzlu systémem každý úkol.

## <a name="summary"></a>Souhrn

Podpora přenos šablonu a soubor aktuálně byly přidány pouze pro rozhraní příkazového řádku Azure. Cílem je rozšíření skupiny, do které můžete použít Batch uživatelům, kteří nemusíte vývoj kódu pomocí rozhraní API služby Batch, například výzkumných pracovníků, IT uživatelé a tak dále. Bez kódování, uživatelům se znalostí Azure Batch a aplikace, které se má spustit Batch můžete vytvořit šablony pro vytvoření fondu a úlohy. Parametry šablony uživatelé bez podrobné údaje o Batch a aplikace pomocí šablony.

Vyzkoušet rozšíření Batch pro rozhraní příkazového řádku Azure a poskytnout nám žádné zpětnou vazbu nebo návrhy, buď v komentářích k tomuto článku nebo prostřednictvím [Batch komunity úložišti](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Další postup

- Naleznete v příspěvku blogu šablony Batch: [úloh systémem Azure Batch pomocí rozhraní příkazového řádku Azure – vyžaduje žádný kód](https://azure.microsoft.com/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Podrobnou dokumentaci instalaci a použití, ukázky a zdrojového kódu jsou k dispozici v [úložiště Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).
