---
title: Průběžná integrace a doručování pro pracovní prostor synapse
description: Naučte se používat průběžnou integraci a doručování k nasazení změn v pracovním prostoru z jednoho prostředí (vývoj, testování, produkce) do jiného.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561953"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Průběžná integrace a doručování pro Azure synapse Workspace

## <a name="overview"></a>Přehled

Průběžná integrace (CI) je proces automatizace sestavení a testování kódu pokaždé, když člen týmu potvrdí změny ve správě verzí. Průběžné nasazování (CD) je proces sestavení, testování, konfigurace a nasazení z několika testovacích nebo přípravných prostředí do produkčního prostředí.

Pro pracovní prostor Azure synapse můžete průběžnou integraci a doručování (CI/CD) přesunout všechny entity z jednoho prostředí (vývoj, testování, produkce) do jiného. Pokud chcete svůj pracovní prostor zvýšit na jiný pracovní prostor, existují dvě části: použití [šablon Azure Resource Manager](../../azure-resource-manager/templates/overview.md) k vytvoření nebo aktualizaci prostředků pracovního prostoru (fondů a pracovního prostoru). Migrujte artefakty (skripty SQL, Poznámkový blok, definice úlohy Spark, kanály, datové sady, toky dat atd.) pomocí nástrojů CI/CD synapse v Azure DevOps. 

V tomto článku se dozvíte, jak pomocí kanálu pro vydávání Azure automatizovat nasazení pracovního prostoru synapse do více prostředí.

## <a name="prerequisites"></a>Požadavky

-   Pracovní prostor použitý pro vývoj byl nakonfigurován s úložištěm Git v nástroji Studio, viz [Správa zdrojového kódu v synapse studiu](source-control.md).
-   Projekt Azure DevOps se připravil pro běh kanálu vydávání verzí.

## <a name="set-up-a-release-pipelines"></a>Nastavení kanálů vydání

1.  V [Azure DevOps](https://dev.azure.com/)otevřete projekt vytvořený pro vydání verze.

1.  Na levé straně stránky vyberte **kanály** a pak vyberte **vydané verze**.

    ![Výběr kanálů, vydání](media/create-release-1.png)

1.  Vyberte **Nový kanál**, nebo pokud už máte kanály, vyberte **Nový** a pak **Nový kanál verze**.

1.  Vyberte **prázdnou šablonu úlohy** .

    ![Vybrat prázdnou úlohu](media/create-release-select-empty.png)

1.  Do pole **název fáze** zadejte název vašeho prostředí.

1.  Vyberte **Přidat artefakt** a pak vyberte úložiště Git nakonfigurované pro vývoj synapse Studio. Vyberte úložiště Git, které jste použili pro správu šablon ARM fondů a pracovního prostoru. Pokud jako zdroj použijete GitHub, budete muset vytvořit připojení služby pro svůj účet GitHub a úložiště Pull. Další informace o [připojení služby](/azure/devops/pipelines/library/service-endpoints) 

    ![Přidat větev publikování](media/release-creation-github.png)

1.  Vyberte větev šablony ARM pro aktualizace prostředků. U **výchozí verze** vyberte možnost **nejnovější z výchozí větve**.

    ![Přidat šablonu ARM](media/release-creation-arm-branch.png)

1.  Vyberte [větev publikování](source-control.md#configure-publishing-settings) úložiště pro **výchozí větev**. Ve výchozím nastavení je tato větev publikování `workspace_publish` . U **výchozí verze** vyberte možnost **nejnovější z výchozí větve**.

    ![Přidání artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Nastavení úlohy fáze pro vytvoření a aktualizaci prostředku ARM 

Přidejte úlohu nasazení Azure Resource Manager pro vytvoření nebo aktualizaci prostředků, včetně pracovního prostoru a fondů:

1. V zobrazení fáze vyberte možnost **Zobrazit úlohy fáze**.

    ![Zobrazení fáze](media/release-creation-stage-view.png)

1. Vytvoří nový úkol. Vyhledejte **nasazení šablony ARM** a pak vyberte **Přidat**.

1. V úloze nasazení vyberte předplatné, skupinu prostředků a umístění pro cílový pracovní prostor. V případě potřeby zadejte přihlašovací údaje.

1. V seznamu **Akce** vyberte **vytvořit nebo aktualizovat skupinu prostředků**.

1. Vyberte tlačítko se třemi tečkami (**...**) vedle pole **Šablona** . Vyhledat šablonu Azure Resource Manager svého cílového pracovního prostoru

1. Vybrat **...** vedle pole **parametry šablony** pro výběr souboru parametrů.

1. Vybrat **...** vedle pole **parametry šablony přepsání** a zadejte požadované hodnoty parametrů pro cílový pracovní prostor. 

1. Vyberte **přírůstkový** **režim nasazení**.
    
    ![nasazení pracovního prostoru a fondů](media/pools-resource-deploy.png)

1. Volitelné Přidejte **Azure PowerShell** pro přiřazení role pracovní prostor udělení a aktualizace. Pokud k vytvoření pracovního prostoru synapse použijete kanál vydaných verzí, bude objekt služby kanálu přidán jako výchozí správce pracovního prostoru. Můžete spustit PowerShell pro udělení přístupu k pracovnímu prostoru jiným účtům. 
    
    ![udělit oprávnění](media/release-creation-grant-permission.png)

 > [!WARNING]
> V režimu úplného nasazení se **odstraní** prostředky, které jsou ve skupině prostředků, ale nejsou zadané v nové šabloně správce prostředků. Další informace najdete v tématu [režimy nasazení Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md) .

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Nastavení úlohy fáze pro nasazení artefaktů 

Pomocí rozšíření [nasazení pracovního prostoru synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) nasaďte další položky v pracovním prostoru synapse, jako je datová sada, skript SQL, Poznámkový blok, definice úlohy Spark, tok Dataflow, kanál, propojená služba, přihlašovací údaje a IR (Integration Runtime).  

1. Vyhledejte a získejte rozšíření z **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Získat rozšíření](media/get-extension-from-market.png)

1. Vyberte organizaci pro instalaci rozšíření. 

     ![Instalace rozšíření](media/install-extension.png)

1. Ujistěte se, že instančnímu objektu kanálu služby Azure DevOps bylo udělené oprávnění k předplatnému a také přiřazeno jako správce pracovního prostoru pro cílový pracovní prostor. 

1. Vytvoří nový úkol. Vyhledejte **nasazení v pracovním prostoru synapse** a pak vyberte **Přidat**.

     ![Přidat rozšíření](media/add-extension-task.png)

1.  V úloze vyberte.. **.** vedle pole **Šablona** vyberte soubor šablony.

1. Vybrat **...** vedle pole **parametry šablony** pro výběr souboru parametrů.

1. Vyberte připojení, skupinu prostředků a název cílového pracovního prostoru. 

1. Vybrat **...** vedle pole **parametry šablony přepsání** a zadejte požadované hodnoty parametrů pro cílový pracovní prostor. 

    ![Nasazení pracovního prostoru synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Ve scénářích CI/CD musí být typ prostředí Integration runtime (IR) v různých prostředích stejný. Například pokud máte v prostředí pro vývoj v místním prostředí IR, stejný IR musí být také typu v místním prostředí v jiných prostředích, jako je například test a produkce. Podobně pokud sdílíte prostředí Integration runtime v několika fázích, je nutné nakonfigurovat prostředí Integration runtime jako propojená místně ve všech prostředích, jako je vývoj, testování a produkce.

## <a name="create-release-for-deployment"></a>Vytvořit vydání pro nasazení 

Po uložení všech změn můžete vybrat **vytvořit vydání** a ručně vytvořit vydání. Informace o automatizaci vytváření vydání najdete v tématu [triggery vydané verze Azure DevOps](/azure/devops/pipelines/release/triggers) .

   ![Vyberte vytvořit vydání.](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>Použití vlastních parametrů šablony pracovního prostoru 

Používáte automatizované CI/CD a chcete změnit některé vlastnosti během nasazení, ale vlastnosti nejsou ve výchozím nastavení parametrizované. V takovém případě můžete přepsat výchozí šablonu parametru.

Chcete-li přepsat výchozí šablonu parametru, je nutné vytvořit vlastní šablonu parametrů, soubor s názvem **template-parameters-definition.js** v kořenové složce větve spolupráce Git. Je nutné použít tento přesný název souboru. Při publikování z větve pro spolupráci bude v pracovním prostoru synapse tento soubor číst a použít jeho konfiguraci k vygenerování parametrů. Pokud se nenajde žádný soubor, použije se výchozí šablona parametru.

### <a name="custom-parameter-syntax"></a>Vlastní syntaxe parametru

Níže jsou uvedeny pokyny pro vytvoření souboru vlastních parametrů:

* Zadejte cestu k vlastnosti pod odpovídajícím typem entity.
* Nastavením názvu vlastnosti `*` určíte, že chcete parametrizovat všechny vlastnosti (pouze na první úrovni, ne rekurzivně). V této konfiguraci můžete také zadat výjimky.
* Nastavení hodnoty vlastnosti jako řetězce označuje, že chcete vlastnost parametrizovat. Použijte formát `<action>:<name>:<stype>`.
   *  `<action>` může to být jeden z těchto znaků:
      * `=` znamená, že aktuální hodnota je nastavená jako výchozí hodnota pro parametr.
      * `-` znamená, že neuchovává výchozí hodnotu parametru.
      * `|` je speciální případ pro tajné klíče z Azure Key Vault pro připojovací řetězce nebo klíče.
   * `<name>` je název parametru. Pokud je prázdný, převezme název vlastnosti. Pokud hodnota začíná `-` znakem, název se zkrátí. Například `AzureStorage1_properties_typeProperties_connectionString` by byl zkrácen na `AzureStorage1_connectionString` .
   * `<stype>` je typ parametru. Pokud `<stype>` je prázdné, výchozí typ je `string` . Podporované hodnoty: `string` , `securestring` , `int` , `bool` , `object` a `secureobject` `array` .
* Zadání pole v souboru indikuje, že vlastnost Matching v šabloně je pole. Synapse projde všemi objekty v poli pomocí zadané definice. Druhý objekt, řetězec, se zobrazí jako název vlastnosti, která se používá jako název parametru pro každou iteraci.
* Definice nemůže být specifická pro instanci prostředku. Každá definice se vztahuje na všechny prostředky daného typu.
* Ve výchozím nastavení jsou všechny zabezpečené řetězce, jako jsou Key Vault tajné klíče a zabezpečené řetězce, jako jsou připojovací řetězce, klíče a tokeny, parametrizované.

### <a name="parameter-template-definition-samples"></a>Ukázky definice šablon parametrů 

Tady je příklad toho, co vypadá definice šablony parametru jako:

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Zde je vysvětlení, jak je předchozí šablona vytvořena, rozdělená podle typu prostředku.

#### <a name="notebooks"></a>Notebooks 

* Vlastnost v cestě `properties/bigDataPool/referenceName` je parametrizovaná s výchozí hodnotou. Pro každý soubor poznámkového bloku můžete parametrizovat připojený fond Spark. 

#### <a name="sql-scripts"></a>Skripty SQL 

* Vlastnosti (Pool a databaseName) v cestě `properties/content/currentConnection` jsou parametrizované jako řetězce bez výchozích hodnot v šabloně. 

#### <a name="pipelines"></a>Pipelines

* Vlastnost v cestě `activities/typeProperties/waitTimeInSeconds` je parametrizovaná. Všechny aktivity v kanálu, které mají vlastnost na úrovni kódu s názvem `waitTimeInSeconds` (například `Wait` aktivita), jsou parametrizované jako číslo s výchozím názvem. V šabloně Správce prostředků ale nebude mít výchozí hodnotu. Během nasazení Správce prostředků se bude jednat o povinný vstup.
* Podobně je vlastnost s názvem `headers` (například v `Web` aktivitě) Parametrizovaná s typem `object` (Object). Má výchozí hodnotu, která je stejná jako hodnota zdrojové továrny.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Všechny vlastnosti v cestě `typeProperties` jsou parametrizované s příslušnými výchozími hodnotami. Například existují dvě vlastnosti v části `IntegrationRuntimes` vlastnosti typu: `computeProperties` a `ssisProperties` . Oba typy vlastností jsou vytvořeny s příslušnými výchozími hodnotami a typy (Object).

#### <a name="triggers"></a>Aktivační události

* V rámci `typeProperties` jsou parametrizované dvě vlastnosti. První z nich je `maxConcurrency` , který má mít výchozí hodnotu a je typu `string` . Má výchozí název parametru `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Vlastnost také je parametrizovaná. V takovém případě jsou všechny vlastnosti na dané úrovni parametrizované jako řetězce s výchozími hodnotami a názvy parametrů. Výjimka je `interval` vlastnost, která je parametrizovaná jako typ `int` . Název parametru je s příponou `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Podobně tato `freq` vlastnost je řetězec a je parametrizovaná jako řetězec. `freq`Vlastnost je však Parametrizovaná bez výchozí hodnoty. Název je zkrácen a přípona. Například, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Propojené služby jsou jedinečné. Vzhledem k tomu, že propojené služby a datové sady mají široké rozsahy typů, můžete zadat vlastní nastavení pro konkrétní typ. V tomto příkladu se použije konkrétní šablona pro všechny propojené služby typu `AzureDataLakeStore` . Pro všechny ostatní (přes `*` ) se použije jiná šablona.
* `connectionString`Vlastnost bude parametrizovaná jako `securestring` hodnota. Nebude mít výchozí hodnotu. Bude mít zkrácený název parametru, který je s příponou `connectionString` .
* Tato vlastnost se `secretAccessKey` stane `AzureKeyVaultSecret` (například v propojené službě Amazon S3). Je automaticky Parametrizovaná jako Azure Key Vault tajný klíč a načítá se z nakonfigurovaného trezoru klíčů. Můžete také parametrizovat samotný Trezor klíčů.

#### <a name="datasets"></a>Datové sady

* I když je pro datové sady k dispozici přizpůsobení specifické pro typ, můžete zadat konfiguraci bez explicitní \* Konfigurace na úrovni. V předchozím příkladu jsou parametrizované všechny vlastnosti datové sady v části `typeProperties` .


## <a name="best-practices-for-cicd"></a>Osvědčené postupy pro CI/CD

Pokud používáte integraci Git s vaším pracovním prostorem synapse a máte kanál CI/CD, který přesouvá vaše změny z vývoje do testu a následně do produkčního prostředí, doporučujeme tyto osvědčené postupy:

-   **Integrace Gitu**. Nakonfigurujte jenom váš pracovní prostor vývojového synapse s integrací Gitu. Změny pracovních prostorů testů a výroby se nasazují prostřednictvím CI/CD a nepotřebují integraci Git.
-   **Příprava fondů před migrací artefaktů**. Pokud máte skript SQL nebo Poznámkový blok připojený ke fondům v pracovním prostoru pro vývoj, očekává se stejný název fondů v různých prostředích. 
-   **Infrastruktura jako kód (IAC)**. Správa infrastruktury (sítí, virtuálních počítačů, nástrojů pro vyrovnávání zatížení a topologie připojení) v popisném modelu používá stejnou verzi jako DevOps tým používá ke zdrojovému kódu. 
-   **Ostatní**. Podívejte se [na osvědčené postupy pro artefakty ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) .

## <a name="troubleshooting-artifacts-deployment"></a>Řešení potíží s nasazením artefaktů 

### <a name="use-the-synapse-workspace-deployment-task"></a>Použití úlohy nasazení v pracovním prostoru synapse

V synapse existuje několik artefaktů, které nejsou prostředky ARM. To se liší od Azure Data Factory. Úloha nasazení šablony ARM nebude správně fungovat, aby se nasadily artefakty synapse.
 
### <a name="unexpected-token-error-in-release"></a>Neočekávaná chyba tokenu ve vydané verzi

Pokud soubor parametrů obsahuje hodnoty parametrů, které nejsou uvozeny řídicími znaky, kanál vydání nebude moci analyzovat soubor a vygeneruje chybu, "neočekávaný token". Doporučujeme, abyste přepsali parametry nebo používali Azure webrecovery k načtení hodnot parametrů. Jako alternativní řešení můžete použít také dvojité řídicí znaky.
