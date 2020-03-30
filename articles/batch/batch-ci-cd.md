---
title: Použití Azure Pipelines k vytváření & nasazení řešení HPC – Azure Batch
description: Zjistěte, jak nasadit kanál sestavení nebo vydání pro aplikaci HPC spuštěnou v Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533126"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Použití Azure Pipelines k vytváření a nasazování řešení HPC

Služby Azure DevOps poskytují řadu nástrojů používaných vývojovými týmy při vytváření vlastní aplikace. Nástroje poskytované Azure DevOps se můžou promítnout do automatizovaného vytváření a testování vysoce výkonných výpočetních řešení. Tento článek ukazuje, jak nastavit průběžnou integraci (CI) a průběžné nasazení (CD) pomocí Azure Pipelines pro vysoce výkonné výpočetní řešení nasazené v Azure Batch.

Azure Pipelines poskytuje řadu moderních procesů CI/CD pro vytváření, nasazování, testování a monitorování softwaru. Tyto procesy urychlují doručování softwaru, což vám umožní soustředit se na kód, nikoli na podporu infrastruktury a provozu.

## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

V tomto příkladu vytvoříme kanál sestavení a vydání pro nasazení infrastruktury Azure Batch a uvolnění balíčku aplikace. Za předpokladu, že kód je vyvinut místně, toto je obecný tok nasazení:

![Diagram znázorňující tok nasazení v našem kanálu](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Nastavení

Chcete-li postupovat podle kroků v tomto článku, budete potřebovat organizaci Azure DevOps a týmový projekt.

* [Vytvoření organizace Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Vytvoření projektu v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Řízení zdrojového kódu pro vaše prostředí

Řízení zdrojového kódu umožňuje týmům sledovat změny provedené v základu kódu a zkontrolovat předchozí verze kódu.

Správa zdrojového kódu se obvykle uvažuje o ručním se softwarovým kódem. A co základní infrastruktura? Tím se dostáváme k infrastruktuře jako kódu, kde budeme používat šablony Azure Resource Manageru nebo jiné open source alternativy k deklarativně definovat naši základní infrastrukturu.

Tato ukázka silně závisí na řadě šablon Správce prostředků (dokumenty JSON) a existujících binárních souborů. Tyto příklady můžete zkopírovat do úložiště a převést je do Azure DevOps.

Struktura základu kódu použitá v této ukázce se podobá následujícímu;

* Složka **s předlohami,** která obsahuje několik šablon Azure Resource Manageru. Šablony jsou vysvětleny v tomto článku.
* Složka **klient-aplikace,** což je kopie [Azure Batch .NET zpracování souborů s ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) ukázkou. To není nutné pro tento článek.
* **Složka aplikace hpc,** což je 64bitová verze [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)systému Windows .
* Složka **potrubí.** Obsahuje soubor YAML popisující náš proces sestavení. To je popsáno v článku.

Tato část předpokládá, že jste obeznámeni se správu verzí a navrhováním šablon Správce prostředků. Pokud nejste obeznámeni s těmito koncepty, další informace naleznete na následujících stránkách.

* [Co je ovládací prvek zdroj?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Principy struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Tento příklad využívá více šablon Správce prostředků k nasazení našeho řešení. K tomu používáme řadu šablon schopností (podobně jako jednotky nebo moduly), které implementují určitou část funkce. Používáme také šablonu komplexního řešení, která je zodpovědná za spojení těchto základních funkcí. Existuje několik výhod tohoto přístupu:

* Základní šablony schopností lze individuálně testovat jednotkové.
* Základní šablony schopností lze definovat jako standard uvnitř organizace a znovu je použít ve více řešeních.

V tomto příkladu je šablona komplexního řešení (deployment.json), která nasazuje tři šablony. Základní šablony jsou šablony schopností, které jsou zodpovědné za nasazení konkrétního aspektu řešení.

![Příklad propojené struktury šablon pomocí šablon Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

První šablona, na kterou se podíváme, je pro účet úložiště Azure. Naše řešení vyžaduje účet úložiště k nasazení aplikace do našeho dávkového účtu. Stojí za to vědět o [referenční příručce k šabloně Správce prostředků správce prostředků pro typy prostředků Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) při vytváření šablon Správce prostředků pro účty úložiště.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Dále se podíváme na šablonu dávkového účtu Azure. Dávkový účet Azure funguje jako platforma pro spouštění mnoha aplikací napříč fondy (seskupení počítačů). Při vytváření šablon Správce prostředků pro dávkové účty stojí za to znát [příručku pro referenční šablony Správce prostředků správce prostředků.](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Další šablona ukazuje příklad vytvoření fondu dávek Azure (back-endové počítače pro zpracování našich aplikací). Při vytváření šablon Správce prostředků pro fondy dávkových účtů stojí za to znát [příručku reference šablony Správce prostředků správce prostředků pro Microsoft.Batch.](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Nakonec máme šablonu, která funguje podobně jako orchestrátor. Tato šablona je zodpovědná za nasazení šablon schopností.

Další informace o [vytváření propojených šablon Azure Resource Manageru](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) najdete také v samostatném článku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>Řešení HPC

Infrastrukturu a software lze definovat jako kód a společně nasílat ve stejném úložišti.

Pro toto řešení se ffmpeg používá jako balíček aplikace. Balíček ffmpeg si můžete stáhnout [zde](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Příklad struktury úložiště Git](media/batch-ci-cd/git-repository.jpg)

V tomto úložišti jsou čtyři hlavní části:

* Složka **arm-templates,** která ukládá naši infrastrukturu jako kód
* Složka **aplikace hpc,** která obsahuje binární soubory pro ffmpeg
* Složka **potrubí,** která obsahuje definici našeho kanálu sestavení.
* **Volitelné**: Složka **klient-aplikace,** která by uklápěla kód pro aplikaci .NET. Nepoužíváme to v ukázce, ale ve vašem vlastním projektu, můžete chtít spustit spuštění hpc dávkové aplikace prostřednictvím klientské aplikace.

> [!NOTE]
> Toto je pouze jeden příklad struktury základu kódu. Tento přístup se používá pro účely prokázání, že aplikace, infrastruktury a kódu kanálu jsou uloženy ve stejném úložišti.

Teď, když je nastaven zdrojový kód, můžeme začít první sestavení.

## <a name="continuous-integration"></a>Kontinuální integrace

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)v rámci služeb Azure DevOps services vám pomůže implementovat kanál sestavení, testování a nasazení pro vaše aplikace.

V této fázi kanálu testy jsou obvykle spuštěny k ověření kódu a sestavení příslušné části softwaru. Počet a typy testů a všechny další úlohy, které spustíte, budou záviset na širší strategii sestavení a vydání.

## <a name="preparing-the-hpc-application"></a>Příprava aplikace HPC

V tomto příkladu se zaměříme na složku **hpc-application.** Složka **aplikace hpc** je software ffmpeg, který bude spuštěn z účtu Azure Batch.

1. Přejděte do části Sestavení v Azure Pipelines ve vaší organizaci Azure DevOps. Vytvořte **nový kanál**.

    ![Vytvoření nového kanálu sestavení](media/batch-ci-cd/new-build-pipeline.jpg)

1. Máte dvě možnosti pro vytvoření kanálu sestavení:

    a. [Použití vizuálního návrháře](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Chcete-li použít, klikněte na "Použít vizuální návrhář" na stránce **Nový kanál.**

    b. [Použití sestavení YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Nový kanál YAML můžete vytvořit kliknutím na možnost Azure Repos nebo GitHub na stránce Nový kanál. Případně můžete uložit níže uvedený příklad do správy zdrojového kódu a odkazovat na existující soubor YAML kliknutím na Visual Designer a potom pomocí šablony YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Jakmile je sestavení nakonfigurováno podle potřeby, vyberte **uložit & fronty**. Pokud máte povolenou průběžnou integraci (v části **Aktivační události),** sestavení se automaticky aktivuje při vytvoření nového potvrzení do úložiště, které splňuje podmínky nastavené v sestavení.

    ![Příklad existujícího kanálu sestavení](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Podívejte se na živé aktualizace o průběhu vašeho sestavení v Azure DevOps přechodem do části **sestavení** Azure Pipelines. Vyberte příslušné sestavení z definice sestavení.

    ![Zobrazení živých výstupů z vašeho sestavení](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Pokud používáte klientskou aplikaci ke spuštění dávkové aplikace HPC, je třeba vytvořit samostatnou definici sestavení pro tuto aplikaci. V dokumentaci k Azure Pipelines najdete řadu [návodů.](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)

## <a name="continuous-deployment"></a>Průběžné nasazování

Azure Pipelines se taky používají k nasazení vaší aplikace a základní infrastruktury. [Kanály vydání](https://docs.microsoft.com/azure/devops/pipelines/release) je součást, která umožňuje průběžné nasazení a automatizuje proces vydání.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Nasazení aplikace a základní infrastruktury

Existuje několik kroků zapojených do nasazení infrastruktury. Jak jsme použili [propojené šablony](../azure-resource-manager/templates/linked-templates.md), tyto šablony budou muset být přístupné z veřejného koncového bodu (HTTP nebo HTTPS). Může se jednalo o úložiště na GitHubu, účet úložiště objektů blob Azure nebo jiné umístění úložiště. Nahrané artefakty šablony mohou zůstat zabezpečené, protože mohou být uchovávány v soukromém režimu, ale přístupné pomocí tokenu sdíleného přístupového podpisu (SAS). Následující příklad ukazuje, jak nasadit infrastrukturu se šablonami z objektu blob úložiště Azure.

1. Vytvořte **novou definici verze**a vyberte prázdnou definici. Poté musíme nově vytvořené prostředí přejmenovat na něco, co je důležité pro náš plynovod.

    ![Kanál počáteční verze](media/batch-ci-cd/Release-0.jpg)

1. Vytvořte závislost na kanálu sestavení, abyste získali výstup pro naši aplikaci HPC.

    > [!NOTE]
    > Ještě jednou si poznamenejte **zdrojový alias**, protože to bude potřeba při vytváření úkolů uvnitř definice verze.

    ![Vytvoření odkazu artefaktu na hpcapplicationpackage v příslušném kanálu sestavení](media/batch-ci-cd/Release-1.jpg)

1. Vytvořte odkaz na jiný artefakt, tentokrát Azure Repo. To je nutné pro přístup k šablonám Správce prostředků uloženým ve vašem úložišti. Vzhledem k tomu, že šablony Správce prostředků nevyžadují kompilaci, není nutné je protlačit kanálem sestavení.

    > [!NOTE]
    > Ještě jednou si poznamenejte **zdrojový alias**, protože to bude potřeba při vytváření úkolů uvnitř definice verze.

    ![Vytvoření odkazu artefaktu na azure úložiště](media/batch-ci-cd/Release-2.jpg)

1. Přejděte do části **proměnných.** Doporučujeme vytvořit v kanálu několik proměnných, abyste nezadávali stejné informace do více úkolů. Jedná se o proměnné použité v tomto příkladu a jejich vliv na nasazení.

    * **applicationStorageAccountName**: Název účtu úložiště pro uložení binárních souborů aplikace HPC
    * **batchAccountApplicationName**: Název aplikace v dávkovém účtu Azure
    * **batchAccountName**: Název dávkového účtu Azure
    * **batchAccountPoolName**: Název fondu virtuálních účtů, který provádí zpracování
    * **batchApplicationId**: Jedinečné ID pro aplikaci Azure Batch
    * **batchApplicationVersion**: Sémantická verze dávkové aplikace (to znamená ffmpeg binární soubory)
    * **umístění**: Umístění prostředků Azure, které mají být nasazeny
    * **resourceGroupName**: Název skupiny prostředků, která má být vytvořena, a kde budou nasazeny prostředky
    * **storageAccountName**: Název účtu úložiště pro uložení propojených šablon Správce prostředků

    ![Příklad proměnných nastavených pro vydání Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Přejděte na úkoly pro prostředí Dev. V níže uvedeném snímku můžete zobrazit šest úkolů. Tyto úkoly budou: stáhnout soubory ffmpeg zip, nasadit účet úložiště pro hostování vnořených šablon Správce prostředků, zkopírovat tyto šablony Správce prostředků do účtu úložiště, nasadit dávkový účet a požadované závislosti, vytvořit aplikaci v účet Azure Batch a nahrajte balíček aplikace do dávkového účtu Azure.

    ![Příklad úloh použitých k uvolnění aplikace HPC do Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Přidejte úlohu **Stahovat artefakt kanálu (náhled)** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Stáhnout balíček aplikace agentovi
    * **Název artefaktu ke stažení:** hpc-aplikace
    * **Cesta ke stažení na**: $(System.DefaultWorkingDirectory)

1. Vytvořte účet úložiště pro uložení artefaktů. Existující účet úložiště z řešení by mohl být použit, ale pro samostatný vzorek a izolaci obsahu vytváříme vyhrazený účet úložiště pro naše artefakty (konkrétně šablony Správce prostředků).

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení účtu úložiště pro šablony Správce prostředků
    * **Předplatné Azure:** Výběr příslušného předplatného Azure
    * **Akce**: Vytvoření nebo aktualizace skupiny prostředků
    * **Skupina zdrojů**: $(resourceGroupName)
    * **Umístění:**$(umístění)
    * **Šablona**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Přepsat parametry šablony**: -accountName $(storageAccountName)

1. Nahrajte artefakty ze správy zdrojového kódu do účtu úložiště. K provedení je úloha azure kanálu. Jako součást tohoto úkolu adresu URL kontejneru účtu úložiště a token SAS lze výstup na proměnnou v Azure Pipelines. To znamená, že může být znovu použit v průběhu této fáze agenta.

    Přidejte úlohu **Azure File Copy** a nastavte následující vlastnosti:
    * **Zdroj:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Typ připojení Azure:** Správce prostředků Azure
    * **Předplatné Azure:** Výběr příslušného předplatného Azure
    * **Typ cíle:** Objekt blob Azure
    * **Účet úložiště RM**: $(storageAccountName)
    * **Název kontejneru**: šablony
    * **Identifikátor URI kontejneru úložiště**: templateContainerUri
    * **Token SAS kontejneru úložiště:** templateContainerSasToken

1. Nasaďte šablonu orchestrator. Odvolání šablony orchestrator z předchozích, zjistíte, že tam byly parametry pro adresu URL kontejneru účtu úložiště, kromě tokenu SAS. Měli byste si všimnout, že proměnné požadované v šabloně Správce prostředků jsou drženy v části proměnné definice verze nebo byly nastaveny z jiné úlohy Azure Pipelines (například část úlohy Azure Blob Copy).

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení dávky Azure
    * **Předplatné Azure:** Výběr příslušného předplatného Azure
    * **Akce**: Vytvoření nebo aktualizace skupiny prostředků
    * **Skupina zdrojů**: $(resourceGroupName)
    * **Umístění:**$(umístění)
    * **Šablona**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Přepsat parametry šablony**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Běžnou praxí je použití úloh azure key vault. Pokud instanční objekt (připojení k vašemu předplatnému Azure) má příslušnou sadu zásad přístupu, můžete stáhnout tajné kódy z trezoru klíčů Azure a použít jako proměnné ve vašem kanálu. Název tajného klíče bude nastaven s přidruženou hodnotou. Například tajný klíč sshPassword může být odkazován s $(sshPassword) v definici verze.

1. Další kroky volají azure cli. První se používá k vytvoření aplikace v Azure Batch. a nahrávat související balíčky.

    Přidejte úlohu rozhraní příkazového příkazu **Konstatování Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Vytvoření aplikace v dávkovém účtu Azure
    * **Předplatné Azure:** Výběr příslušného předplatného Azure
    * **Umístění skriptu**: Vložkový skript
    * **Vsazený skript**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Druhý krok se používá k nahrání přidružených balíčků do aplikace. V našem případě, ffmpeg soubory.

    Přidejte úlohu rozhraní příkazového příkazu **Konstatování Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nahrání balíčku do dávkového účtu Azure
    * **Předplatné Azure:** Výběr příslušného předplatného Azure
    * **Umístění skriptu**: Vložkový skript
    * **Vsazený skript**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Číslo verze balíčku aplikace je nastaveno na proměnnou. To je vhodné, pokud přepsání předchozí verze balíčku funguje pro vás a pokud chcete ručně řídit číslo verze balíčku tlačil do Azure Batch.

1. Vytvořte novou verzi výběrem **možnosti Release > Vytvořit novou verzi**. Po spuštění vyberte odkaz na novou verzi a zobrazte stav.

1. Živý výstup z agenta můžete zobrazit výběrem tlačítka **Protokoly** pod prostředím.

    ![Zobrazení stavu vydání](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testování životního prostředí

Po nastavení prostředí potvrďte, že následující testy lze úspěšně dokončit.

Připojte se k novému dávkovému účtu Azure pomocí příkazového řádku Azure z příkazového řádku Prostředí PowerShell.

* Přihlaste se ke `az login` svému účtu Azure pomocí a postupujte podle pokynů k ověření.
* Nyní ověřte účet Batch:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Seznam dostupných aplikací

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Zkontrolujte, zda je fond platný

```azurecli
az batch pool list
```

Poznamenejte `currentDedicatedNodes` si hodnotu z výstupu tohoto příkazu. Tato hodnota je upravena v dalším testu.

#### <a name="resize-the-pool"></a>Změna velikosti bazénu

Změňte velikost fondu tak, aby byly k dispozici výpočetní uzly pro testování úloh a úloh, zkontrolujte pomocí příkazu seznam fondu, abyste viděli aktuální stav, dokud není změna velikosti dokončena a nejsou k dispozici uzly.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Další kroky

Kromě tohoto článku existují dva kurzy, které využívají ffmpeg, pomocí .NET a Python. Další informace o interakci s dávkovým účtem prostřednictvím jednoduché aplikace najdete v těchto kurzech.

* [Spuštění paralelníúlohy s Azure Batch pomocí rozhraní Python API](tutorial-parallel-python.md)
* [Spuštění paralelníúlohy s Azure Batch pomocí rozhraní .NET API](tutorial-parallel-dotnet.md)
