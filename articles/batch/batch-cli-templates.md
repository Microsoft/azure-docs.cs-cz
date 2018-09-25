---
title: Spouštění úloh služby Azure Batch začátku do konce pomocí šablon | Dokumentace Microsoftu
description: Vytvořte fondy, úlohy a úkoly služby Batch pomocí souborů šablon a Azure CLI.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 08/02/2018
ms.author: danlep
ms.openlocfilehash: 753a36eb6fb7a0c007c62bbab7fe7390e706b1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964288"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Použití šablon rozhraní příkazového řádku Azure Batch a přenos souborů

Pomocí služby Azure Batch rozšíření rozhraní příkazového řádku Azure, je možné spouštět úlohy Batch bez psaní kódu.

Vytvoření a použití souborů šablon JSON pomocí Azure CLI vytvořit fondy, úlohy a úkoly služby Batch. Použijte příkazy rozšíření rozhraní příkazového řádku můžete snadno nahrávat vstupní soubory úloh do účtu úložiště přidružené k účtu Batch a úloha stažení výstupních souborů.

## <a name="overview"></a>Přehled

Rozšíření Azure CLI umožňuje Batch se používá koncový uživatelé, kteří nejsou vývojáři. Jenom příkazy rozhraní příkazového řádku můžete vytvořit fond, nahrání vstupních dat, vytvoření úlohy a přidružené úlohy a stáhnout výsledný výstupní data. Není vyžadován žádný další kód. Přímo spouštět příkazy rozhraní příkazového řádku nebo systém integrovat do skriptů.

Vytváření šablon služby batch [stávající podporu služby Batch v Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) pro soubory JSON k určení hodnoty vlastností při vytváření fondů, úloh, úlohy a další položky. Šablon služby batch, přidejte následující funkce:

-   Parametry se dají definovat. Při použití této šablony pouze hodnoty parametrů jsou určeny k vytvoření položky, s jinými hodnotami vlastností položka zadaná v těle šablony. Uživatel, který se rozumí služby Batch a aplikace, které chcete spustit pomocí služby Batch můžete vytvořit šablony, zadání fondu, úlohy a hodnoty vlastností úlohy. Méně neznáte služby Batch a/nebo aplikací potřebuje uživatel jen k určení hodnot definovaných parametrů.

-   Továrny úloh úloh vytvořte jednu nebo více úloh, které jsou přidružené úlohy, takže není nutné pro mnoho definic úloh vytvoření a odeslání úlohy výrazně zjednodušuje.


Úlohy obvykle použít vstupních datových souborů a vytvoří výstupní datové soubory. Účet úložiště je spojený ve výchozím nastavení ke každému účtu Batch. Přenos souborů do a z tohoto účtu úložiště pomocí rozhraní příkazového řádku se žádné psaní kódu a žádné přihlašovací údaje úložiště.

Například [ffmpeg](http://ffmpeg.org/) je Oblíbené aplikace, která zpracovává audio a video soubory. Tady je postup pomocí příkazového řádku Azure Batch pro vyvolání aplikaci ffmpeg kvůli překódování zdrojových souborů videa pro různá řešení.

-   Vytvoření šablony fondu. Uživatel vytvářející šablony ví, jak volat aplikace ffmpeg a jejich požadavcích; Určí, že příslušný operační systém, virtuální počítač velikost, jak aplikace ffmpeg se instalované (z balíčku aplikace nebo pomocí Správce balíčků, například) a jiné hodnoty vlastností fondu. Parametry jsou vytvořeny tak, že při použití této šablony je potřeba zadat ID fondu a počtu virtuálních počítačů.

-   Vytvořte šablonu úlohy. Vytvoření šablony uživatel ví, jak se aplikace ffmpeg musí zavolat překódování zdroje videa k jiným rozlišením a určuje příkazový řádek úkolu; také ví, že je složka obsahující zdroj videosouborů, s úlohou na vstupní soubor.

-   Koncový uživatel se sadou video soubory k překódování nejprve vytvoří fond pomocí šablony fondu zadáte jenom ID fondu a počet požadovaných virtuálních počítačů. Zdrojové soubory jsou pak můžete nahrát do překódování. Úloha pak budete moct odeslat pomocí šablony projektu, zadáte jenom ID fondu a umístění zdrojových souborů, nahraje. Úlohy služby Batch se vytvoří s jeden úkol na vstupní soubor generován. Nakonec můžete stáhnout překódovaná výstupní soubory.

## <a name="installation"></a>Instalace

Nejprve nainstalovat rozšíření rozhraní příkazového řádku Azure Batch [nainstalovat rozhraní příkazového řádku Azure CLI 2.0](/cli/azure/install-azure-cli), nebo můžete spouštět v Azure CLI [Azure Cloud Shell](../cloud-shell/overview.md).

Nainstalujte nejnovější verzi rozšíření služby Batch pomocí následujícího příkazu rozhraní příkazového řádku Azure:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Další informace o rozšíření rozhraní příkazového řádku služby Batch a další možnosti instalace najdete v článku [úložiště GitHub se vzorovými](https://github.com/Azure/azure-batch-cli-extensions).


Pokud chcete používat funkce rozšíření rozhraní příkazového řádku, budete potřebovat účet Azure Batch a pro příkazy, které přenos souborů do a z úložiště, propojený účet úložiště.

Přihlaste se do účtu Batch pomocí rozhraní příkazového řádku Azure, najdete v článku [Správa prostředků služby Batch pomocí rozhraní příkazového řádku Azure](batch-cli-get-started.md).

## <a name="templates"></a>Šablony

Šablony Azure Batch jsou podobné šablon Azure Resource Manageru v syntaxi a funkce. Jsou soubory JSON, které obsahují položky názvy a hodnoty vlastností, ale přidejte následující hlavní koncepty:

-   **Parametry**

    -   Povolit hodnoty vlastností, které v části textu, zadaným pouze hodnoty parametrů museli zadat při použití této šablony. Například dokončení definice fondu můžete umístit do těla a pouze jeden parametr definované pro id fondu; pouze řetězec ID fondu proto musí být zadán k vytvoření fondu.
        
    -   Text šablony můžete vytvořené uživatelem s znalostí v oblasti služby Batch a aplikace, které chcete spustit dávkový; Při použití této šablony je nutné zadat pouze hodnoty pro parametry definované Autor. Uživatel bez podrobných Batch a/nebo aplikaci knowledge lze proto použít šablony.

-   **Proměnné**

    -   Povolit parametr jednoduchých nebo složitých hodnot pro zadaný na jednom místě a použít na jeden nebo více místech v těle šablony. Proměnné můžete zjednodušit a zmenšit velikost šablony, jakož i usnadňují snadněji spravovatelné tím, že chcete-li změnit vlastnosti jednoho umístění.

-   **Konstrukce vyšší úrovně**

    -   Některé konstrukce vyšší úrovně jsou k dispozici v šabloně, které ještě nejsou k dispozici v rozhraní API služby Batch. Například objekt pro vytváření úloh lze definovat v šabloně projektu, který vytváří více úkolů pro úlohu pomocí běžné definice úlohy. Tyto konstrukce nemuseli kódu dynamicky vytvořit více souborů JSON, jako je například jeden soubor na jeden úkol, stejně jako můžete vytvořit soubory skriptů k instalaci aplikací přes Správce balíčků.

    -   V určitém okamžiku může být tato konstrukce přidané do služby Batch a k dispozici v rozhraní API služby Batch, uživatelská rozhraní, atd.

### <a name="pool-templates"></a>Fond šablony

Fond šablony podporují standardní šablony funkce parametry a proměnné. Podporují také následující konstrukce vyšší úrovně:

-   **Odkazy na balíček**

    -   Umožňuje volitelně softwaru, které se mají zkopírovat na uzly fondu pomocí Správce balíčků. Správce balíčků a ID balíčku jsou uvedeny. Deklarací jeden nebo více balíčků, můžete vyhnout se vytváření skriptu, který načte požadované balíčky, instalaci skriptu a spuštění skriptu na všech uzlech fondu.

Následuje příklad šablony, která vytvoří fond virtuálních počítačů s Linuxem pomocí aplikace ffmpeg nainstalované. Pro použití je třeba zadejte pouze řetězec ID fondu a počtu virtuálních počítačů ve fondu:

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

Pokud byl název souboru šablony _fondu ffmpeg.json_, vyvolejte šablony následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Rozhraní příkazového řádku vás vyzve k zadání hodnot pro `poolId` a `nodeCount` parametry. Můžete také zadat parametry v souboru JSON. Příklad:

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

Pokud byl název souboru JSON parametrů *fondu parameters.json*, vyvolejte šablony následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Šablony projektu

Šablony úloh podporují možnosti standardní šablonu parametry a proměnné. Podporují také následující konstrukce vyšší úrovně:

-   **Továrny úloh**

    -   Vytvoří několik úkolů pro úlohu z definice jeden úkol. Tři typy továrny úloh podporují – čištění oblouku úloh na soubor a kolekce úkolů.

Následuje příklad šablony, která vytvoří úlohu, která překódování MP4 videa souborů pomocí aplikace ffmpeg na jednu z dvou nižší rozlišení. Vytvoří jeden úkol na zdrojový soubor videa. V tématu [skupiny souborů a souborů přenos](#file-groups-and-file-transfer) pro další informace o skupinách souboru pro vstup a výstup úlohy.

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

Pokud byl název souboru šablony _úlohy ffmpeg.json_, vyvolejte šablony následujícím způsobem:

```azurecli
az batch job create --template job-ffmpeg.json
```

Jako dříve, rozhraní příkazového řádku vás vyzve k zadání hodnot parametrů. Můžete také zadat parametry v souboru JSON.

### <a name="use-templates-in-batch-explorer"></a>Použití šablon v Průzkumníkovi služby Batch

Můžete nahrát šablonu rozhraní příkazového řádku služby Batch a [Batch Explorer](https://github.com/Azure/BatchExplorer) aplikace klasické pracovní plochy (dříve nazývané BatchLabs) k vytvoření fondu služby Batch nebo úlohy. Můžete také vybrat z předdefinovaných šablon fond a úlohu ve galerii služby Batch Explorer.

Nahrání šablony:

1. V Průzkumníkovi služby Batch, vyberte **Galerie** > **místní šablony**.

2. Vyberte nebo přetáhněte, místní fond nebo šablonu úloh.

3. Vyberte **pomocí této šablony**a postupujte podle pokynů na obrazovce zobrazí výzvu.

## <a name="file-groups-and-file-transfer"></a>Skupiny souborů a přenos souborů

Většina úloh a úkolů vyžadují vstupních souborů a vytvoří výstupní soubory. Obvykle vstupních a výstupních souborů přenese, od klienta k uzlu, nebo z uzlu do klienta. Rozšíření Azure Batch CLI abstrahuje přenos nepřítomnosti souborů, které využívá účet úložiště, který přidružíte ke každému účtu Batch.

Skupina souborů je kontejner, který se vytvoří v účtu úložiště Azure. Skupina souborů mohou obsahovat podsložky.

Rozšíření rozhraní příkazového řádku služby Batch poskytuje příkazy pro ukládání souborů z klienta do zadaného souboru skupiny a stahovat soubory ze zadaného souboru skupiny do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Fond a úlohu šablony umožňují soubory uložené v souboru skupiny pro kopírování na uzly fondu nebo vypnutí fondu uzlů zpět ke skupině souborů. Například v rámci úlohy zadaná šablona dříve, skupiny souborů *ffmpeg vstup* je zadaný pro objekt pro vytváření úloh jako umístění zdrojových souborů videa zkopírují do uzlu pro překódování. Skupina souborů *ffmpeg výstup* je umístění, kam se zkopíroval překódovaná výstupních souborů z uzlu spuštění každé úlohy.

## <a name="summary"></a>Souhrn

Podporu převodu šablony a souborů se přidaly aktuálně pouze na rozhraní příkazového řádku Azure. Cílem je rozšířit cílovou skupinu, můžete uživatele, kteří není potřeba vývoj kódu pomocí rozhraní API služby Batch, jako je například výzkumní pracovníci a IT uživatelům služby Batch. Bez psaní kódu, uživatelé se znalostí Azure Batch a aplikace, které chcete spustit pomocí služby Batch můžete vytvořit šablony pro vytvoření fondu a úlohy. Parametry šablony uživatele bez podrobné poznatky, Batch a aplikace pomocí šablony.

Vyzkoušejte si rozšíření služby Batch pro Azure CLI a uveďte názory a návrhy, buď v komentářích pro účely tohoto článku nebo prostřednictvím [Batch komunity úložiště](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Další postup

- Jsou k dispozici v podrobnou instalaci a použití dokumentaci, ukázky a zdrojový kód [úložiště Azure GitHub](https://github.com/Azure/azure-batch-cli-extensions).

- Další informace o používání [Batch Explorer](https://github.com/Azure/BatchExplorer) k vytváření a správě prostředků služby Batch.
