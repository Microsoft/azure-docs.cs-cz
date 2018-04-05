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
ms.openlocfilehash: c991a1535b82a76a3d0b9bdbf4ede8f3e22bc866
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Použití šablon rozhraní příkazového řádku služby Batch a přenos souborů (Preview)

Pomocí rozhraní příkazového řádku Azure je možné spustit úlohy Batch bez nutnosti psaní kódu.

Vytvořit a použít soubory šablon pomocí rozhraní příkazového řádku Azure k vytvoření dávky fondy, úlohy a úkoly. Vstupní soubory úlohy můžete snadno nahrán do účtu úložiště přidruženého k dávkového účtu a úlohy výstupní soubory stáhnout.

## <a name="overview"></a>Přehled

Rozšíření pro rozhraní příkazového řádku Azure umožňuje Batch být použité kompletní uživatelé, kteří nejsou vývojáři. Fond lze vytvořit, nahrán vstupních dat, úlohy a přidružených úloh vytvoření a výsledný výstupní data stáhnout – potřeba žádný kód, rozhraní příkazového řádku používá přímo, nebo se integrovaný do skriptů.

Vytvoření šablony batch ve [existující Batch podpora v rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/batch/batch-cli-get-started#json-files-for-resource-creation) umožňuje soubory JSON k určení hodnoty vlastností pro vytváření fondů, úloh, úlohy a další položky. S šablonami Batch jsou přidány následující možnosti přes co je možné pomocí soubory JSON:

-   Parametry lze definovat. Pokud se použije šablona, jsou pro vytvoření položky s jiné položky hodnoty vlastností specifikované v těle šablony zadat pouze hodnoty parametru. Uživatel, který jste srozuměni s tím Batch a aplikace, které se má spustit Batch můžete vytvořit šablony, fond, úlohy a hodnoty vlastností úlohy. Menší neznáte Batch nebo aplikace potřebuje uživatel jen zadat hodnoty pro parametry definované.

-   Objekty pro vytváření úloh úlohy vytvořit jeden nebo více úkoly spojené s úlohu, zabraňující potřebu mnoho definic úloh vytvoření a výrazně zjednodušit úlohy.


Vstupní data soubory musí být zadaný pro úlohy a často vytváří výstupní datové soubory. Je přidružený účet úložiště, ve výchozím nastavení se každého účtu Batch a soubory je možné snadno přenést do a z tohoto účtu úložiště pomocí rozhraní příkazového řádku bez kódování a vyžadují přihlašovací údaje žádné úložiště.

Například [ffmpeg](http://ffmpeg.org/) je Oblíbené aplikace, která zpracovává soubory audia a videa. Rozhraní příkazového řádku Azure Batch můžete použít k vyvolání ffmpeg převeďte zdroje videosoubory pro různá řešení.

-   Je-li vytvořit šablonu fondu. Uživatel vytváření šablony umí volání aplikace ffmpeg a požadavky na jeho; určí příslušné operačního systému, počítač velikost, jak ffmpeg je nainstalovaná (z balíčku aplikace nebo pomocí Správce balíčků, například) a dalších fond hodnoty vlastností. Parametry jsou vytvořeny tak, že pokud se použije šablona, je potřeba zadat ID fondu a počet virtuálních počítačů.

-   Je-li vytvořit šablonu úlohy. Uživatel vytváření šablony vědět, jak se ffmpeg musí být volána převod zdroj videa na jiné rozlišení a určuje příkazový řádek úkolu; také věděli, že je složka obsahující zdrojové soubory videa, s úloha vyžaduje pro vstupní soubor.

-   Koncový uživatel se sadou videosouborů převod nejprve vytvoří fond pomocí šablony fondu, zadáte jenom ID fondu a počet virtuálních počítačů, které jsou potřeba. Převod se pak můžete nahrát zdrojové soubory. Úlohy lze odeslat pak pomocí šablony úlohy, zadáte jenom ID fondu a umístění zdrojových souborů nahrát. Jeden úkol za vstupní soubor generován se vytvoří dávkovou úlohu. Nakonec převodu výstupních souborů může být stahování.

## <a name="installation"></a>Instalace

Možnosti přenosu šablonu a soubor vyžadují rozšíření k instalaci.

Pokyny o tom, jak nainstalovat rozhraní příkazového řádku Azure najdete v tématu [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Po instalaci rozhraní příkazového řádku Azure, může být nainstalována nejnovější verze rozšíření Batch, pomocí následujícího příkazu příkazového řádku:

```azurecli
az extension add --source https://github.com/Azure/azure-batch-cli-extensions/releases/download/azure-batch-cli-extensions-2.0.1/azure_batch_cli_extensions-2.0.1-py2.py3-none-any.whl
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

    -   Povolit parametr jednoduché nebo komplexní hodnoty zadané v jednom místě a použít na jeden nebo více místech v těle šablony. Proměnné můžete zjednodušit a zmenšit velikost šablony, a také zkontrolujte více udržovatelný tak, že jedno umístění, jehož hodnota může změnit vlastnosti chcete změnit.

-   **Konstrukce vyšší úrovně**

    -   Některé konstrukce vyšší úrovně jsou k dispozici v šabloně, které ještě nejsou k dispozici v rozhraní API služby Batch. Například objekt pro vytváření úloh lze definovat v šabloně úlohy, která vytvoří více úloh pro úlohu pomocí společná definice úlohy. Tyto konstrukce nemuseli kód dynamicky vytvořte několik souborů JSON, jako je například jeden soubor pro úlohy, a také vytvořit skript soubory k instalaci aplikací prostřednictvím Správce balíčků, například.

    -   V určitém okamžiku kde je to možné, tyto konstrukce může být přidány do služby Batch a k dispozici v rozhraní API služby Batch, uživatelská atd.

### <a name="pool-templates"></a>Šablony fondu

Kromě možností standardní šablona parametry a proměnné jsou podporovány následující konstrukce vyšší úrovně šablonou fondu:

-   **Odkazy na balíček**

    -   Umožňuje volitelně softwaru zkopírují na uzly fondu pomocí Správce balíčků. Správce balíčků a ID balíčku nejsou zadány. Schopnost deklarovat jeden nebo více balíčků zabraňuje potřeba vytvořit skript, který získá požadované balíčky, nainstalujte skript a spusťte skript na všech uzlech fondu.

Následuje příklad šablony, která vytvoří fond virtuálních počítačů Linux s ffmpeg nainstalovaná a pouze vyžaduje řetězec ID fondu a počet virtuálních počítačů k dodání použít:

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
                    "sku": "16.04.0-LTS",
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

Pokud se název souboru šablony _fondu ffmpeg.json_, pak šablona by být volána následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

### <a name="job-templates"></a>Šablony úloh

Kromě možností standardní šablona parametry a proměnné jsou podporovány následující konstrukce vyšší úrovně šablonou úlohy:

-   **Objekt pro vytváření úloh**

    -   Vytvoří více úkolů pro úlohu z definice jeden úkol. Tři typy objektu pro vytváření úloh jsou podporovány – čištění oblouku úloh na soubor a kolekce úloh.

Následuje příklad šablony, která vytvoří úlohu, která používá ffmpeg převod MP4 videosoubory na jednu ze dvou nižší rozlišení, s vytvořenými pro jednotlivé zdroje videosoubor úloh:

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

Pokud se název souboru šablony _úlohy ffmpeg.json_, pak šablona by být volána následujícím způsobem:

```azurecli
az batch job create --template job-ffmpeg.json
```

## <a name="file-groups-and-file-transfer"></a>Skupiny souborů a přenos souborů

Většina úloh a úloh vyžadují vstupní soubory a vytvoří výstupní soubory. Obě soubory vstupní a výstupní soubory je obvykle nutné převést, od klienta k uzlu, nebo z uzlu do klienta. Rozšíření rozhraní příkazového řádku Azure Batch abstrahuje přenos nepřítomnosti souborů a využívá účet úložiště, který se vytvoří ve výchozím nastavení pro každý účet Batch.

Skupinu souborů rovná kontejner, který je vytvořen v účtu úložiště Azure. Skupina souborů může mít podsložky.

Rozšíření rozhraní příkazového řádku Batch poskytuje příkazy pro odesílání souborů z klienta do skupiny zadaný soubor a stahování souborů z zadané skupině souborů klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Šablony fondu a úlohy umožňují soubory uložené v souboru skupiny pro kopírování na uzly fondu nebo vypnout uzly fondu zpět do skupiny souborů. Například v šabloně projektů dříve zadaný soubor skupiny "ffmpeg – vstup" je zadán pro objekt pro vytváření úloh jako umístění zdrojových souborů video zkopírovat dolů na uzel pro překódování; soubor skupiny "ffmpeg-output" se používá jako umístění, kde se převodu výstupní soubory zkopírují do uzlu systémem každý úkol.

## <a name="summary"></a>Souhrn

Podpora přenos šablonu a soubor aktuálně byly přidány pouze pro rozhraní příkazového řádku Azure. Cílem je rozšíření skupiny, do které můžete použít Batch uživatelům, kteří nemusíte vývoj kódu pomocí rozhraní API služby Batch, například výzkumných pracovníků, IT uživatelé a tak dále. Bez kódování, uživatelům se znalostí Azure Batch a aplikace, které se má spustit Batch můžete vytvořit šablony pro vytvoření fondu a úlohy. Parametry šablony uživatelé bez podrobné údaje o Batch a aplikace pomocí šablony.

Vyzkoušet rozšíření Batch pro rozhraní příkazového řádku Azure a poskytnout nám žádné zpětnou vazbu nebo návrhy, buď v komentářích k tomuto článku nebo prostřednictvím [fórum Azure Batch](https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch).

## <a name="next-steps"></a>Další postup

- Naleznete v příspěvku blogu šablony Batch: [úloh systémem Azure Batch pomocí rozhraní příkazového řádku Azure – vyžaduje žádný kód](https://azure.microsoft.com/en-us/blog/running-azure-batch-jobs-using-the-azure-cli-no-code-required/).
- Podrobnou dokumentaci instalaci a použití, ukázky a zdrojového kódu jsou k dispozici v [úložiště Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).
