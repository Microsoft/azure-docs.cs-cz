---
title: Průběžná integrace a doručování pro pracovní prostor synapse
description: Naučte se používat průběžnou integraci a doručování k nasazení změn v pracovním prostoru z jednoho prostředí (vývoj, testování, produkce) do jiného.
author: liudan66
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 833478d956560c981bd6cc3ba03b48bb602f563c
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739669"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Průběžná integrace a doručování pro Azure synapse Workspace

## <a name="overview"></a>Přehled

Průběžná integrace (CI) je proces automatizace sestavení a testování kódu pokaždé, když člen týmu potvrdí změny ve správě verzí. Průběžné nasazování (CD) je proces sestavení, testování, konfigurace a nasazení z několika testovacích nebo přípravných prostředí do produkčního prostředí.

V pracovním prostoru Azure synapse Analytics se průběžná integrace a doručování (CI/CD) přesouvá všechny entity z jednoho prostředí (vývoj, testování, produkce) do jiného. Pokud chcete svůj pracovní prostor zvýšit na jiný pracovní prostor, existují dvě části. Nejprve pomocí [šablony Azure Resource Manager (šablona ARM)](../../azure-resource-manager/templates/overview.md) můžete vytvořit nebo aktualizovat prostředky pracovního prostoru (fondy a pracovní prostor). Pak migrujte artefakty (skripty SQL, Poznámkový blok, definice úlohy Spark, kanály, datové sady, toky dat atd.) pomocí nástrojů CI/CD Azure synapse Analytics v Azure DevOps. 

Tento článek popisuje, jak pomocí kanálu vydaných verzí Azure DevOps automatizovat nasazení pracovního prostoru Azure synapse do více prostředí.

## <a name="prerequisites"></a>Požadavky

Tyto požadavky a konfigurace musí být na místě pro automatizaci nasazení pracovního prostoru Azure synapse do více prostředí.

### <a name="azure-devops"></a>Azure DevOps

- Projekt Azure DevOps byl připraven ke spuštění kanálu vydávání verzí.
- [Udělte všem uživatelům, kteří budou vracet kód "základní" přístup na úrovni organizace](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page), aby viděli úložiště.
- Udělte vlastnictví úložiště Azure synapse oprávnění vlastníka.
- Ujistěte se, že jste vytvořili samoobslužný agent virtuálního počítače Azure DevOps nebo používáte hostovaného agenta Azure DevOps.
- Oprávnění k [vytvoření připojení služby Azure Resource Manager pro skupinu prostředků](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml).
- Správce služby Azure Active Directory (Azure AD) musí [do organizace Azure DevOps nainstalovat Deployment Agent rozšíření pracovního prostoru Azure DevOps synapse](/azure/devops/marketplace/install-extension).
- Vytvořte nebo pojmenujte existující účet služby, ve kterém se má kanál spustit. Místo účtu služby můžete použít osobní přístupový token, ale vaše kanály nebudou po odstranění uživatelského účtu fungovat.

### <a name="azure-active-directory"></a>Azure Active Directory

- V Azure AD vytvořte instanční objekt, který se má použít pro nasazení. Úloha nasazení pracovního prostoru synapse nepodporuje použití spravované identity v verze 1 * a starších.
- Pro tuto akci jsou vyžadována práva správce Azure AD.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> Tyto požadavky můžete automatizovat a nasadit pomocí stejného kanálu, šablony ARM nebo rozhraní příkazového řádku Azure CLI, ale tento proces není popsaný v tomto článku.

- Pracovní prostor zdroj, který se používá pro vývoj, musí být nakonfigurovaný s úložištěm Git v synapse studiu. Další informace najdete v tématu [Správa zdrojového kódu v synapse studiu](source-control.md#configuration-method-2-manage-hub).

- Prázdný pracovní prostor, do kterého se má nasazovat. Nastavení prázdného pracovního prostoru:

  1. Vytvořte nový pracovní prostor Azure synapse Analytics.
  1. Udělte agentovi virtuálního počítače a oprávnění přispěvatele instančního objektu skupinu prostředků, ve které je hostovaný nový pracovní prostor.
  1. V novém pracovním prostoru nekonfigurujte připojení úložiště Git.
  1. V Azure Portal Najděte nový pracovní prostor Azure synapse Analytics a udělte si sami a ten, kdo spustí práva vlastníka pracovního prostoru Azure synapse Analytics kanálu Azure DevOps. 
  1. Přidejte agenta virtuálního počítače Azure DevOps a instanční objekt do role přispěvatele pro daný pracovní prostor (Tato hodnota by se měla dědit, ale ověřit, jestli je).
  1. V pracovním prostoru Azure synapse Analytics otevřete do **studia**  >  **Správa**  >  **IAM**. Přidejte agenta virtuálního počítače Azure DevOps a instanční objekt do skupiny Admins v pracovním prostoru.
  1. Otevřete účet úložiště, který se používá pro pracovní prostor. V nástroji IAM přidejte agenta virtuálního počítače a instanční objekt do role Přispěvatel dat objektu BLOB pro výběr úložiště.
  1. V rámci předplatného podpory vytvořte Trezor klíčů a ujistěte se, že stávající pracovní prostor i nový pracovní prostor mají k trezoru aspoň oprávnění získat a zobrazit seznam.
  1. Aby automatizované nasazení fungovalo, ujistěte se, že všechny připojovací řetězce, které jsou zadané v propojených službách, jsou v trezoru klíčů.

### <a name="additional-prerequisites"></a>Další požadavky
 
 - Fondy Spark a prostředí Integration runtime v místním prostředí nejsou v kanálu vytvořené. Pokud máte propojenou službu, která používá místní prostředí Integration runtime, vytvořte ji ručně v novém pracovním prostoru.
 - Pokud vyvíjíte poznámkové bloky a máte je připojené k fondu Spark, vytvořte v pracovním prostoru znovu fond Spark.
 - Poznámkové bloky propojené s fondem Spark, které neexistují v prostředí, se nebudou nasazovat.
 - Názvy fondů Spark musí být v obou pracovních prostorech stejné.
 - Pojmenujte všechny databáze, fondy SQL a další prostředky, které jsou v obou pracovních prostorech stejné.
 - Pokud jsou zřízené fondy SQL pozastavené při pokusu o nasazení, nasazení může selhat.

Další informace najdete v tématu [CI CD ve službě Azure synapse Analytics část 4 – kanál pro vydávání verzí](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434). 


## <a name="set-up-a-release-pipeline"></a>Nastavení kanálu pro vydání

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

## <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>Nastavení úlohy fáze pro šablonu ARM k vytvoření a aktualizaci prostředku 

Pokud máte šablonu ARM k nasazení prostředku, jako je pracovní prostor Azure synapse, Spark a SQL, nebo Trezor klíčů, přidejte úlohu nasazení Azure Resource Manager pro vytvoření nebo aktualizaci těchto prostředků:

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

## <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>Nastavení úlohy fáze pro nasazení artefaktů synapse 

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

1. Vybrat **...** vedle pole **přepsat parametry šablony** a zadejte požadované hodnoty parametrů pro cílový pracovní prostor včetně připojovacích řetězců a klíčů účtů, které se používají ve vašich propojených službách. [Další informace získáte kliknutím sem]. (https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)

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

Pokud používáte integraci Git s vaším pracovním prostorem Azure synapse a máte kanál CI/CD, který přesouvá vaše změny z vývoje do testu a následně do produkčního prostředí, doporučujeme tyto osvědčené postupy:

-   **Integrace Gitu**. Nakonfigurujte jenom svůj vývojový pracovní prostor Azure synapse s integrací Gitu. Změny pracovních prostorů testů a výroby se nasazují prostřednictvím CI/CD a nepotřebují integraci Git.
-   **Příprava fondů před migrací artefaktů**. Pokud máte skript SQL nebo Poznámkový blok připojený ke fondům v pracovním prostoru pro vývoj, očekává se stejný název fondů v různých prostředích. 
-   **Infrastruktura jako kód (IAC)**. Správa infrastruktury (sítí, virtuálních počítačů, nástrojů pro vyrovnávání zatížení a topologie připojení) v popisném modelu používá stejnou verzi jako DevOps tým používá ke zdrojovému kódu. 
-   **Ostatní**. Podívejte se [na osvědčené postupy pro artefakty ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd) .

## <a name="troubleshooting-artifacts-deployment"></a>Řešení potíží s nasazením artefaktů 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>Použití úlohy nasazení pracovního prostoru Azure synapse Analytics

Ve službě Azure synapse Analytics existuje několik artefaktů, které nejsou prostředky ARM. To se liší od Azure Data Factory. Úloha nasazení šablony ARM nebude správně fungovat, aby se nasadily artefakty Azure synapse Analytics.
 
### <a name="unexpected-token-error-in-release"></a>Neočekávaná chyba tokenu ve vydané verzi

Pokud soubor parametrů obsahuje hodnoty parametrů, které nejsou uvozeny řídicími znaky, kanál vydání nebude moci analyzovat soubor a vygeneruje chybu, "neočekávaný token". Doporučujeme přepsat parametry nebo použít Azure Key Vault k načtení hodnot parametrů. Jako alternativní řešení můžete použít také dvojité řídicí znaky.
