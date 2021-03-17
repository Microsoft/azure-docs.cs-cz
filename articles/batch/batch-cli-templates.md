---
title: Kompletní spouštění úloh pomocí šablon
description: Když máte jenom příkazy rozhraní příkazového řádku, můžete vytvořit fond, nahrávat vstupní data, vytvářet úlohy a související úlohy a stahovat výsledná výstupní data.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 845a32c2feda5a5a3b8d44d237c62db94cae1779
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848717"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Použití Azure Batch šablon CLI a přenosu souborů

Pomocí rozšíření služby Batch v Azure CLI je možné spouštět dávkové úlohy bez psaní kódu.

Vytvořte a použijte soubory šablon JSON pomocí Azure CLI a vytvořte tak fondy, úlohy a úkoly služby Batch. Pomocí příkazů rozšíření CLI snadno nahrajte vstupní soubory úlohy do účtu úložiště přidruženého k účtu Batch a stáhněte výstupní soubory úlohy.

> [!NOTE]
> Soubory JSON nepodporují stejné funkce jako [šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Jsou určeny k formátování jako nezpracovaný text žádosti REST. Rozšíření CLI nemění žádné existující příkazy, ale má podobnou možnost šablony, která přidává funkce částečného Azure Resource Manager šablony. Viz rozšíření rozhraní příkazového [řádku Azure Batch pro Windows, Mac a Linux](https://github.com/Azure/azure-batch-cli-extensions).

## <a name="overview"></a>Přehled

Rozšíření Azure CLI umožňuje službě Batch používat koncové uživatele, kteří nejsou vývojáři. Když máte jenom příkazy rozhraní příkazového řádku, můžete vytvořit fond, nahrávat vstupní data, vytvářet úlohy a související úlohy a stahovat výsledná výstupní data. Není vyžadován žádný další kód. Spusťte příkazy rozhraní příkazového řádku přímo nebo je integrujte do skriptů.

Šablony služby Batch se vytvářejí na stávající podpoře dávek v [Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) pro soubory JSON, které určují hodnoty vlastností při vytváření fondů, úloh, úkolů a dalších položek. Šablony Batch přidávají následující možnosti:

-   Parametry lze definovat. Při použití šablony jsou pro vytvoření položky zadány pouze hodnoty parametrů s dalšími hodnotami vlastnosti položky, které jsou uvedeny v těle šablony. Uživatel, který rozumí dávce a aplikace, které mají být spuštěny pomocí dávky, může vytvořit šablony, určit hodnoty vlastností fondu, úloh a úkolů. Uživatel je méně obeznámený se službou Batch a/nebo aplikace potřebuje zadat jenom hodnoty definovaných parametrů.

-   Továrny úloh úlohy vytvoří jeden nebo více úloh přidružených k úloze a vyhne se tak nutnosti vytvářet mnoho definic úloh a významně zjednodušují odesílání úloh.


Úlohy obvykle využívají vstupní datové soubory a vytváří výstupní datové soubory. Účet úložiště je ve výchozím nastavení spojený s každým účtem Batch. Přeneste soubory z tohoto účtu úložiště a z něj pomocí rozhraní příkazového řádku bez kódování a přihlašovacích údajů k úložišti.

Například [ffmpeg](https://ffmpeg.org/) je oblíbená aplikace, která zpracovává zvukové soubory a videosoubory. Tady jsou kroky, pomocí Azure Batch CLI vyvolat ffmpeg k překódování zdrojových videosouborů do různých rozlišení.

-   Vytvořte šablonu fondu. Uživatel vytvářející šablonu zná způsob volání aplikace FFmpeg a jejích požadavků; určují příslušný operační systém, velikost virtuálního počítače, způsob instalace FFmpeg (z balíčku aplikace nebo správce balíčků, například) a další hodnoty vlastností fondu. Parametry jsou vytvořeny, takže když je šablona použita, je nutné zadat pouze ID fondu a počet virtuálních počítačů.

-   Vytvořte šablonu úlohy. Uživatel vytvářející šablonu ví, jak je nutné vyvolávat ffmpeg k překódování zdrojového videa na jiné rozlišení a určení příkazového řádku úkolu. také se dozvíte, že existuje složka obsahující zdrojové videosoubory s úlohou požadovanou pro každý vstupní soubor.

-   Koncový uživatel, který má sadu videosouborů k překódování, vytvoří fond pomocí šablony fondu a určí jenom ID a počet požadovaných virtuálních počítačů. Pak mohou nahrát zdrojové soubory do kódování. Úlohu je pak možné odeslat pomocí šablony úlohy a zadat jenom ID fondu a umístění nahraných zdrojových souborů. Vytvoří se úloha Batch s jednou úlohou pro vygenerování vstupního souboru. Nakonec lze soubory s překódováním výstupních souborů stáhnout.

## <a name="installation"></a>Instalace

Pokud chcete nainstalovat rozšíření Azure Batch CLI, nejdřív [nainstalujte rozhraní příkazového řádku Azure cli 2,0](/cli/azure/install-azure-cli)nebo spusťte rozhraní příkazového řádku Azure cli v [Azure Cloud Shell](../cloud-shell/overview.md).

Pomocí následujícího příkazu rozhraní příkazového řádku Azure nainstalujte nejnovější verzi rozšíření Batch:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Další informace o rozšíření Batch CLI a dalších možnostech instalace najdete v [úložišti GitHub](https://github.com/Azure/azure-batch-cli-extensions).


Pokud chcete používat funkce rozšíření CLI, potřebujete účet Azure Batch a pro příkazy, které přenášejí soubory do a ze služby Storage, propojeného účtu úložiště.

Pokud se chcete přihlásit k účtu Batch pomocí Azure CLI, přečtěte si téma [Správa prostředků služby Batch pomocí Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Šablony

Šablony Azure Batch se podobají šablonám Azure Resource Manager, a to ve funkcích a syntaxí. Jsou to soubory JSON, které obsahují názvy vlastností položky a hodnoty, ale přidávají následující hlavní koncepty:

-   **Parametry**

    -   Povolí zadání hodnot vlastností v oddílu body, přičemž při použití šablony se musí zadat jenom hodnoty parametrů. Například úplná definice pro fond může být umístěná v těle a jenom jeden parametr definovaný pro `poolId` ; pro vytvoření fondu je potřeba zadat jenom řetězec ID fondu.

    -   Tělo šablony může vytvořit někdo se znalostí o službě Batch a aplikace, které se mají spustit pomocí dávky. Při použití šablony musí být zadány pouze hodnoty pro parametry definované autorem. Uživatel bez podrobných znalostí a/nebo aplikací v dávce může proto šablony použít.

-   **Proměnné**

    -   Povoluje zadání jednoduchých nebo složitých hodnot parametrů na jednom místě a používá se na jednom nebo více místech v těle šablony. Proměnné mohou zjednodušit a snižovat velikost šablony a zvýšit tak jejich udržovatelnost tím, že musí mít jedno umístění ke změně vlastností.

-   **Konstrukce na vyšší úrovni**

    -   V šabloně jsou k dispozici některé konstrukce vyšší úrovně, které ještě nejsou k dispozici v rozhraních API pro Batch. Například objekt pro vytváření úloh lze definovat v šabloně úlohy, která vytváří více úloh pro úlohu pomocí běžné definice úlohy. Tyto konstrukce zabraňují nutnosti vytvářet kód pro dynamické vytváření více souborů JSON, jako je například jeden soubor na úlohu, a také vytvářet soubory skriptu pro instalaci aplikací prostřednictvím Správce balíčků.

    -   V určitém okamžiku mohou být tyto konstrukce přidány do služby Batch a k dispozici v rozhraních API služby Batch, uživatelská rozhraní atd.

### <a name="pool-templates"></a>Šablony fondů

Šablony fondů podporují standardní možnosti šablon parametrů a proměnných. Podporují také následující konstrukci vyšší úrovně:

-   **Odkazy na balíčky**

    -   Volitelně umožňuje, aby se software zkopíroval do uzlů fondu pomocí Správce balíčků. Je zadaný správce balíčků a ID balíčku. Deklarováním jednoho nebo více balíčků se vyhnete vytváření skriptu, který získá požadované balíčky, instalaci skriptu a spuštění skriptu na všech uzlech fondu.

Následuje příklad šablony, která vytváří fond virtuálních počítačů se systémem Linux s nainstalovaným ffmpeg. Pokud ho chcete použít, zadejte jenom řetězec ID fondu a počet virtuálních počítačů ve fondu:

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
            "taskSlotsPerNode": 1,
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

Pokud se soubor šablony jmenoval _pool-ffmpeg.jsv_, vyvolejte šablonu následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Rozhraní příkazového řádku vás vyzve k zadání hodnot `poolId` `nodeCount` parametrů a. Parametry můžete také dodat v souboru JSON. Například:

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

Pokud byl soubor JSON s parametry pojmenován *pool-parameters.jsv*, vyvolejte šablonu následujícím způsobem:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Šablony úloh

Šablony úloh podporují standardní možnosti šablon parametrů a proměnných. Podporují také následující konstrukci vyšší úrovně:

-   **Objekt pro vytváření úloh**

    -   Vytvoří více úloh pro úlohu z definice jedné úlohy. Podporovány jsou tři typy objektu pro vytváření úloh – parametry pro čištění, úlohy a soubory a kolekce úloh.

Následuje příklad šablony, která vytvoří úlohu pro překódování videosouborů MP4 pomocí FFmpeg na jedno ze dvou nižších rozlišení. Vytvoří jeden úkol na zdrojový soubor videa. Další informace o skupinách souborů pro vstup a výstup úlohy najdete v tématu [skupiny souborů a přenos souborů](#file-groups-and-file-transfer) .

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

Pokud se soubor šablony jmenoval _job-ffmpeg.jsv_, vyvolejte šablonu následujícím způsobem:

```azurecli
az batch job create --template job-ffmpeg.json
```

Stejně jako předtím vás rozhraní příkazového řádku vyzve k zadání hodnot pro parametry. Parametry můžete také dodat v souboru JSON.

### <a name="use-templates-in-batch-explorer"></a>Použití šablon v Batch Explorer

Můžete nahrát šablonu Batch CLI do desktopové aplikace [Batch Explorer](https://github.com/Azure/BatchExplorer) (dřív označované jako BatchLabs) a vytvořit fond nebo úlohu Batch. V galerii Batch Explorer můžete také vybrat z předdefinovaných fondů a šablon úloh.

Postup nahrání šablony:

1. V Batch Explorer vyberte **Gallery**  >  **místní šablony**galerie.

2. Vyberte nebo přetáhněte místní fond nebo šablonu úlohy do seznamu.

3. Vyberte **použít tuto šablonu**a postupujte podle pokynů na obrazovce.

## <a name="file-groups-and-file-transfer"></a>Skupiny souborů a přenos souborů

Většina úloh a úloh vyžaduje vstupní soubory a vytváří výstupní soubory. Vstupní soubory a výstupní soubory jsou obvykle předávány, buď z klienta na uzel, nebo z uzlu do klienta. Rozšíření Azure Batch CLI oddělí soubor pryč a využívá účet úložiště, který můžete přidružit ke každému účtu Batch.

Skupina souborů odpovídá kontejneru vytvořenému v účtu služby Azure Storage. Skupina souborů může obsahovat podsložky.

Rozšíření Batch CLI poskytuje příkazy pro nahrání souborů z klienta do zadané skupiny souborů a stažení souborů ze zadané skupiny souborů do klienta.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Šablony fondů a úloh umožňují zadání souborů uložených ve skupinách souborů pro kopírování uzlů fondu nebo mimo uzly fondu zpět do skupiny souborů. Například v šabloně úlohy, která byla zadána dříve, je pro objekt pro vytváření úloh zadána skupina souborů *ffmpeg-Input* jako umístění zdrojových videosouborů, které byly zkopírovány do uzlu pro překódování. Skupina souborů *ffmpeg-Output* je umístění, kde jsou zkopírovány výstupní soubory z uzlu, na kterém každý úkol spouští.

## <a name="summary"></a>Shrnutí

Podpora pro přenos šablon a souborů se v tuto chvíli přidala jenom do Azure CLI. Cílem je rozšířit cílovou skupinu, která může používat dávku uživatelům, kteří nepotřebují vyvíjet kód pomocí rozhraní API pro Batch, jako jsou například výzkumníki a uživatelé IT. Bez psaní kódu můžou uživatelé s poznatky o Azure, službě Batch a aplikacích, které mají spustit pomocí služby Batch, vytvořit šablony pro vytvoření fondu a úloh. S parametry šablony můžou uživatelé bez podrobných znalostí o službě Batch a aplikace používat šablony.

Vyzkoušejte si rozšíření Batch pro rozhraní příkazového řádku Azure CLI a sdělte nám jakékoli názory a návrhy, a to buď v komentářích k tomuto článku, nebo prostřednictvím [úložiště komunity služby Batch](https://github.com/Azure/Batch).

## <a name="next-steps"></a>Další kroky

- Podrobná dokumentace k instalaci a používání, ukázky a zdrojový kód jsou k dispozici v [úložišti GitHub Azure](https://github.com/Azure/azure-batch-cli-extensions).

- Přečtěte si další informace o použití [Batch Explorer](https://github.com/Azure/BatchExplorer) k vytváření a správě prostředků Batch.
