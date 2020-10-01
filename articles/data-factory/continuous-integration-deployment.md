---
title: Průběžná integrace a doručování v Azure Data Factory
description: Naučte se používat průběžnou integraci a doručování k přesunu Data Factory kanálů z jednoho prostředí (vývoj, testování, produkce) do jiného.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 6b091406b15db036007ba6a11049ee63ffe99cf0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616887"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Průběžná integrace a doručování v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Přehled

Nepřetržitá integrace je postup testování každé změny provedené v základu kódu automaticky a co nejdříve.Průběžné doručování se řídí testováním, které se provádí během nepřetržité integrace a vkládání změn do pracovního nebo produkčního systému.

V Azure Data Factory průběžná integrace a doručování (CI/CD) znamená přesun Data Factory kanálů z jednoho prostředí (vývoj, testování, produkce) do jiného. Azure Data Factory využívá [šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) k uložení konfigurace různých entit ADF (kanálů, datových sad, toků dat atd.). Existují dva navrhované metody, jak propagovat datovou továrnu na jiné prostředí:

-    Automatizované nasazení pomocí Data Factory integrace s [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
-    Ručně nahrajte šablonu Správce prostředků pomocí integrace Data Factory UX s Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Životní cyklus CI/CD

Níže najdete ukázkový Přehled životního cyklu CI/CD v objektu pro vytváření dat Azure, který je nakonfigurovaný pomocí Azure Repos Git. Další informace o tom, jak nakonfigurovat úložiště Git, najdete [v tématu Správa zdrojového kódu v Azure Data Factory](source-control.md).

1.  Vývojovou datovou továrnu vytvoří a nakonfiguruje s Azure Repos Git. Všichni vývojáři by měli mít oprávnění k vytváření Data Factorych prostředků, jako jsou kanály a datové sady.

1.  Vývojář [vytvoří větev funkcí](source-control.md#creating-feature-branches) , aby provedla změnu. Spouštějí jejich kanály s nejnovějšími změnami. Další informace o tom, jak ladit spuštění kanálu, najdete v tématu [iterativní vývoj a ladění pomocí Azure Data Factory](iterative-development-debugging.md).

1.  Jakmile je vývojář spokojen s jejich změnami, vytvoří žádost o přijetí změn ze své větve funkcí do hlavní větve nebo do větve pro spolupráci, aby byly změny zkontrolovány partnerskými uzly.

1.  Po schválení žádosti o přijetí změn a jejich sloučení do hlavní větve se změny publikují do továrny pro vývoj.

1.  Když je tým připraven k nasazení změn do objektu test nebo UAT (testování přijetí uživateli), tým přejde na jejich Azure Pipelines vydání a nasadí požadovanou verzi vývojového objektu UAT. Toto nasazení probíhá v rámci úlohy Azure Pipelines a k použití příslušné konfigurace používá Správce prostředků parametrů šablony.

1.  Po ověření změn v továrně testu proveďte nasazení do produkčního továrny pomocí další úlohy vydání kanálů.

> [!NOTE]
> K úložišti Git je přidružená jenom vývojová továrna. Na testovací a výrobní továrny by se neměla přidružit úložiště Git a měla by se aktualizovat jenom prostřednictvím kanálu Azure DevOps nebo pomocí šablony správy prostředků.

Následující obrázek zvýrazňuje různé kroky tohoto životního cyklu.

![Diagram kontinuální integrace s Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizace kontinuální integrace pomocí Azure Pipelinesch verzí

Následuje návod pro nastavení Azure Pipelines vydání, které automatizuje nasazení datové továrny do více prostředí.

### <a name="requirements"></a>Požadavky

-   Předplatné Azure propojené se službou Visual Studio Team Foundation Server nebo Azure Repos, které používá [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Datová továrna konfigurovaná s Azure Repos Integration Git.

-    [Trezor klíčů Azure](https://azure.microsoft.com/services/key-vault/) , který obsahuje tajné klíče pro každé prostředí.

### <a name="set-up-an-azure-pipelines-release"></a>Nastavení verze Azure Pipelines

1.  V [Azure DevOps](https://dev.azure.com/)otevřete projekt, který je nakonfigurovaný s vaší datovou továrnou.

1.  Na levé straně stránky vyberte **kanály**a pak vyberte **vydané verze**.

    ![Výběr kanálů, vydání](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Vyberte **Nový kanál**, nebo pokud už máte kanály, vyberte **Nový** a pak **Nový kanál verze**.

1.  Vyberte **prázdnou šablonu úlohy** .

    ![Vybrat prázdnou úlohu](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Do pole **název fáze** zadejte název vašeho prostředí.

1.  Vyberte **Přidat artefakt**a pak vyberte úložiště Git nakonfigurované pro vaši vývojovou datovou továrnu. Vyberte [větev publikování](source-control.md#configure-publishing-settings) úložiště pro **výchozí větev**. Ve výchozím nastavení je tato větev publikování `adf_publish` . U **výchozí verze**vyberte možnost **nejnovější z výchozí větve**.

    ![Přidání artefaktu](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Přidat úlohu nasazení Azure Resource Manager:

    a.  V zobrazení fáze vyberte možnost **Zobrazit úlohy fáze**.

    ![Zobrazení fáze](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Vytvoří nový úkol. Vyhledejte **nasazení šablony ARM**a pak vyberte **Přidat**.

    c.  V úloze nasazení vyberte předplatné, skupinu prostředků a umístění pro cílovou datovou továrnu. V případě potřeby zadejte přihlašovací údaje.

    d.  V seznamu **Akce** vyberte **vytvořit nebo aktualizovat skupinu prostředků**.

    e.  Vyberte tlačítko se třemi tečkami (**...**) vedle pole **Šablona** . Vyhledejte šablonu Azure Resource Manager, která je generována ve větvi publikování v nakonfigurovaném úložišti Git. Vyhledejte soubor `ARMTemplateForFactory.json` ve <FactoryName> složce adf_publish větve.

    f.  Vybrat **...** vedle pole **parametry šablony** pro výběr souboru parametrů. Vyhledejte soubor `ARMTemplateParametersForFactory.json` ve <FactoryName> složce adf_publish větve.

    například  Vybrat **...** vedle pole **parametry šablony přepsání** a zadejte požadované hodnoty parametrů pro cílovou datovou továrnu. Pro přihlašovací údaje pocházející z Azure Key Vault zadejte název tajného kódu mezi dvojité uvozovky. Pokud je například název tajného klíče cred1, zadejte pro tuto hodnotu **"$ (cred1)"** .

    h. Vyberte **přírůstkový** **režim nasazení**.

    > [!WARNING]
    > V režimu úplného nasazení se **odstraní**prostředky, které jsou ve skupině prostředků, ale nejsou zadané v nové šabloně správce prostředků. Další informace najdete v tématu [režimy nasazení Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md) .

    ![Data Factory výrobního nasazení](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Uložte kanál pro vydávání verzí.

1. Pokud chcete aktivovat vydanou verzi, vyberte **vytvořit vydání**. Informace o automatizaci vytváření vydání najdete v tématu [triggery vydané verze Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers?view=azure-devops) .

   ![Vyberte vytvořit vydání.](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Ve scénářích CI/CD musí být typ prostředí Integration runtime (IR) v různých prostředích stejný. Například pokud máte v prostředí pro vývoj v místním prostředí IR, stejný IR musí být také typu v místním prostředí v jiných prostředích, jako je například test a produkce. Podobně pokud sdílíte prostředí Integration runtime v několika fázích, je nutné nakonfigurovat prostředí Integration runtime jako propojená místně ve všech prostředích, jako je vývoj, testování a produkce.

### <a name="get-secrets-from-azure-key-vault"></a>Získání tajných kódů z Azure Key Vault

Pokud máte tajné kódy, které byste měli předat do šablony Azure Resource Manager, doporučujeme používat Azure Key Vault s Azure Pipelinesou verzí.

Existují dva způsoby, jak pokládat s tajnými kódy:

1.  Přidejte tajné klíče do souboru Parameters. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../azure-resource-manager/templates/key-vault-parameter.md).

    Vytvořte kopii souboru parametrů, který se nahraje do větve publikování. Pomocí tohoto formátu nastavte hodnoty parametrů, které chcete získat z Key Vault:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Když použijete tuto metodu, tajný kód se automaticky načte z trezoru klíčů.

    Soubor parametrů musí být také ve větvi publikování.

1. Přidejte [úlohu Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) před úlohu nasazení Azure Resource Manager popsané v předchozí části:

    1.  Na kartě **úlohy** vytvořte novou úlohu. Vyhledejte **Azure Key Vault** a přidejte ji.

    1.  V úloze Key Vault vyberte předplatné, ve kterém jste Trezor klíčů vytvořili. V případě potřeby zadejte přihlašovací údaje a pak vyberte Trezor klíčů.

    ![Přidat úlohu Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udělit oprávnění agentovi Azure Pipelines

Azure Key Vault úloha může selhat s chybou odepření přístupu, pokud nejsou nastavená správná oprávnění. Stáhněte si protokoly pro vydání a vyhledejte soubor. ps1, který obsahuje příkaz pro udělení oprávnění agentovi Azure Pipelines. Příkaz lze spustit přímo. Případně můžete ze souboru zkopírovat ID objektu zabezpečení a zásadu přístupu přidat ručně v Azure Portal. `Get` a `List` jsou minimální požadovaná oprávnění.

### <a name="updating-active-triggers"></a>Aktualizace aktivních aktivačních událostí

Pokud se pokusíte aktualizovat aktivní aktivační události, může nasazení selhat. Chcete-li aktualizovat aktivní aktivační události, je nutné je ručně zastavit a po nasazení je znovu spustit. Můžete to provést pomocí úlohy Azure PowerShell:

1.  Na kartě **úlohy** ve vydané verzi přidejte úlohu **Azure PowerShell** . Vyberte úlohu verze 4. *. 

1.  Vyberte předplatné, ve kterém je váš objekt pro vytváření.

1.  Jako typ skriptu vyberte **cestu k souboru skriptu** . To vyžaduje uložení skriptu PowerShellu do úložiště. K zastavení aktivačních událostí můžete použít následující skript prostředí PowerShell:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Můžete provést podobný postup (s `Start-AzDataFactoryV2Trigger` funkcí) a restartovat triggery po nasazení.

Tým Data Factory poskytl [ukázkový skript před a po nasazení](#script) , který se nachází na konci tohoto článku. 

## <a name="manually-promote-a-resource-manager-template-for-each-environment"></a>Ruční zvýšení úrovně Správce prostředků šablony pro každé prostředí

1. V seznamu **šablon ARM** vyberte **Exportovat šablonu ARM** a exportujte šablonu správce prostředků pro vaši datovou továrnu ve vývojovém prostředí.

   ![Export šablony Správce prostředků](media/continuous-integration-deployment/continuous-integration-image1.png)

1. V továrnách testovacích a produkčních dat vyberte **Importovat šablonu ARM**. Tato akce přejde do Azure Portal, kde můžete importovat exportovanou šablonu. Vyberte **vytvořit vlastní šablonu v editoru** a otevřete tak editor šablon Správce prostředků.

   ![Vytvoření vlastní šablony](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Vyberte **načíst soubor**a pak vyberte vygenerovanou šablonu správce prostředků. Toto je **arm_template.js** soubor umístěný v souboru zip, který je exportovaný v kroku 1.

   ![Upravit šablonu](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. V části nastavení zadejte hodnoty konfigurace, jako jsou přihlašovací údaje k propojeným službám. Až budete hotovi, vyberte **koupit** a nasaďte šablonu správce prostředků.

   ![Oddíl nastavení](media/continuous-integration-deployment/continuous-integration-image5.png)

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Použití vlastních parametrů s šablonou Resource Manageru

Pokud má vaše továrna pro vývoj přidružené úložiště Git, můžete přepsat výchozí parametry šablony Správce prostředků šablony Správce prostředků vygenerované publikováním nebo exportem šablony. V těchto scénářích možná budete chtít přepsat výchozí šablonu Parametrizace:

* Používáte automatizované CI/CD a chcete změnit některé vlastnosti během nasazení Správce prostředků, ale vlastnosti nejsou ve výchozím nastavení parametrizované.
* Vaše továrna je tak velká, že výchozí šablona Správce prostředků je neplatná, protože má více než maximální povolený počet parametrů (256).

Pokud chcete přepsat výchozí šablonu Parametrizace, klikněte na Centrum správy a v části Správa zdrojového kódu vyberte **šablonu Parametrizace** . Výběrem **Upravit šablonu** otevřete Editor kódu šablony Parametrizace. 

![Správa vlastních parametrů](media/author-management-hub/management-hub-custom-parameters.png)

Vytvořením vlastní šablony Parametrizace se vytvoří soubor s názvem **arm-template-parameters-definition.js** v kořenové složce vaší větve Git. Je nutné použít tento přesný název souboru.

![Soubor vlastních parametrů](media/continuous-integration-deployment/custom-parameters.png)

Při publikování z větve pro spolupráci Data Factory načte tento soubor a použije jeho konfiguraci k vygenerování parametrizovaných vlastností. Pokud se nenajde žádný soubor, použije se výchozí šablona.

Při exportu šablony Správce prostředků Data Factory přečte tento soubor z jakékoli větve, na které aktuálně pracujete, nikoli z větve pro spolupráci. Můžete vytvořit nebo upravit soubor z privátní větve, kde můžete testovat své změny výběrem možnosti **Exportovat šablonu ARM** v uživatelském rozhraní. Pak můžete soubor sloučit do větve pro spolupráci.

> [!NOTE]
> Vlastní šablona Parametrizace nemění limit parametru šablony ARM 256. Umožňuje zvolit a snížit počet parametrizovaných vlastností.

### <a name="custom-parameter-syntax"></a>Vlastní syntaxe parametru

Níže jsou uvedeny některé pokyny, které je třeba provést při vytváření souboru vlastních parametrů **arm-template-parameters-definition.jsv**. Soubor se skládá z oddílu pro každý typ entity: Trigger, kanál, propojená služba, datová sada, prostředí Integration runtime a tok dat.

* Zadejte cestu k vlastnosti pod odpovídajícím typem entity.
* Nastavením názvu vlastnosti  `*` určíte, že chcete parametrizovat všechny vlastnosti (pouze na první úrovni, ne rekurzivně). V této konfiguraci můžete také zadat výjimky.
* Nastavení hodnoty vlastnosti jako řetězce označuje, že chcete vlastnost parametrizovat. Použijte formát  `<action>:<name>:<stype>` .
   *  `<action>` může to být jeden z těchto znaků:
      * `=` znamená, že aktuální hodnota je nastavená jako výchozí hodnota pro parametr.
      * `-` znamená, že neuchovává výchozí hodnotu parametru.
      * `|` je speciální případ pro tajné klíče z Azure Key Vault pro připojovací řetězce nebo klíče.
   * `<name>` je název parametru. Pokud je prázdný, převezme název vlastnosti. Pokud hodnota začíná `-` znakem, název se zkrátí. Například `AzureStorage1_properties_typeProperties_connectionString` by byl zkrácen na `AzureStorage1_connectionString` .
   * `<stype>` je typ parametru. Pokud  `<stype>`   je prázdné, výchozí typ je `string` . Podporované hodnoty: `string` , `bool` , `number` , `object` a `securestring` .
* Zadání pole v definičním souboru znamená, že vlastnost Matching v šabloně je pole. Data Factory projde všemi objekty v poli pomocí definice, která je zadána v objektu Integration runtime daného pole. Druhý objekt, řetězec, se zobrazí jako název vlastnosti, která se používá jako název parametru pro každou iteraci.
* Definice nemůže být specifická pro instanci prostředku. Každá definice se vztahuje na všechny prostředky daného typu.
* Ve výchozím nastavení jsou všechny zabezpečené řetězce, jako jsou Key Vault tajné klíče a zabezpečené řetězce, jako jsou připojovací řetězce, klíče a tokeny, parametrizované.
 
### <a name="sample-parameterization-template"></a>Ukázková šablona Parametrizace

Tady je příklad toho, co může šablona Parametrizace vypadat jako:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
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
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Zde je vysvětlení, jak je předchozí šablona vytvořena, rozdělená podle typu prostředku.

#### <a name="pipelines"></a>Kanály
    
* Vlastnost v cestě `activities/typeProperties/waitTimeInSeconds` je parametrizovaná. Všechny aktivity v kanálu, které mají vlastnost na úrovni kódu s názvem `waitTimeInSeconds` (například `Wait` aktivita), jsou parametrizované jako číslo s výchozím názvem. V šabloně Správce prostředků ale nebude mít výchozí hodnotu. Během nasazení Správce prostředků se bude jednat o povinný vstup.
* Podobně je vlastnost s názvem `headers` (například v `Web` aktivitě) Parametrizovaná s typem `object` (JObject). Má výchozí hodnotu, která je stejná jako hodnota zdrojové továrny.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Všechny vlastnosti v cestě `typeProperties` jsou parametrizované s příslušnými výchozími hodnotami. Například existují dvě vlastnosti v části `IntegrationRuntimes` vlastnosti typu: `computeProperties` a `ssisProperties` . Oba typy vlastností jsou vytvořeny s příslušnými výchozími hodnotami a typy (Object).

#### <a name="triggers"></a>Aktivační procedury

* V rámci `typeProperties` jsou parametrizované dvě vlastnosti. První z nich je `maxConcurrency` , který má mít výchozí hodnotu a je typu `string` . Má výchozí název parametru `<entityName>_properties_typeProperties_maxConcurrency` .
* `recurrence`Vlastnost také je parametrizovaná. V takovém případě jsou všechny vlastnosti na dané úrovni parametrizované jako řetězce s výchozími hodnotami a názvy parametrů. Výjimka je `interval` vlastnost, která je parametrizovaná jako typ `number` . Název parametru je s příponou `<entityName>_properties_typeProperties_recurrence_triggerSuffix` . Podobně tato `freq` vlastnost je řetězec a je parametrizovaná jako řetězec. `freq`Vlastnost je však Parametrizovaná bez výchozí hodnoty. Název je zkrácen a přípona. Například, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Propojené služby jsou jedinečné. Vzhledem k tomu, že propojené služby a datové sady mají široké rozsahy typů, můžete zadat vlastní nastavení pro konkrétní typ. V tomto příkladu se použije konkrétní šablona pro všechny propojené služby typu `AzureDataLakeStore` . Pro všechny ostatní (přes `*` ) se použije jiná šablona.
* `connectionString`Vlastnost bude parametrizovaná jako `securestring` hodnota. Nebude mít výchozí hodnotu. Bude mít zkrácený název parametru, který je s příponou `connectionString` .
* Tato vlastnost se `secretAccessKey` stane `AzureKeyVaultSecret` (například v propojené službě Amazon S3). Je automaticky Parametrizovaná jako Azure Key Vault tajný klíč a načítá se z nakonfigurovaného trezoru klíčů. Můžete také parametrizovat samotný Trezor klíčů.

#### <a name="datasets"></a>Datové sady

* I když je pro datové sady k dispozici přizpůsobení specifické pro typ, můžete zadat konfiguraci bez explicitní \* Konfigurace na úrovni. V předchozím příkladu jsou parametrizované všechny vlastnosti datové sady v části `typeProperties` .

### <a name="default-parameterization-template"></a>Výchozí šablona Parametrizace

Níže je uvedená aktuální výchozí šablona Parametrizace. Pokud potřebujete přidat jenom několik parametrů, může být vhodné upravit přímo tuto šablonu, protože nepřijdete o existující strukturu Parametrizace.

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

### <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>Příklad: Parametrizace existující Azure Databricks interaktivní ID clusteru

Následující příklad ukazuje, jak přidat jednu hodnotu do výchozí šablony Parametrizace. Pro propojenou službu datacihly se souborem parametrů chceme přidat jenom existující Azure Databricks interaktivní ID clusteru. Všimněte si, že tento soubor je stejný jako předchozí soubor s výjimkou sčítání `existingClusterId` v poli Properties (vlastnosti) `Microsoft.DataFactory/factories/linkedServices` .

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Propojené šablony Správce prostředků

Pokud jste pro své datové továrny nastavili CI/CD, můžete překročit omezení šablony Azure Resource Manager, protože vaše továrna roste větší. Například jeden limit je maximální počet prostředků v šabloně Správce prostředků. Aby se při generování úplné šablony Správce prostředků pro objekt pro vytváření vešly velké továrny, Data Factory nyní generuje propojené šablony Správce prostředků. Pomocí této funkce je celá datová část továrny rozdělena do několika souborů, takže omezení neomezíte.

Pokud jste nakonfigurovali Git, propojené šablony se generují a ukládají společně s úplnými Správce prostředků šablonami v adf_publish větvi v nové složce s názvem linkedTemplates:

![Složka propojených šablon Správce prostředků](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Propojené šablony Správce prostředků obvykle sestávají z hlavní šablony a sady podřízených šablon, které jsou propojeny s hlavním serverem. Nadřazená šablona se nazývá ArmTemplate_master.jsv a podřízené šablony jsou pojmenovány se vzorovým ArmTemplate_0.jsna ArmTemplate_1.jszapnuté a tak dále. 

Pokud chcete místo úplné Správce prostředků šablony použít propojené šablony, aktualizujte úlohu CI/CD tak, aby odkazovala na ArmTemplate_master.jsnamísto ArmTemplateForFactory.jszapnuto (úplnou Správce prostředků šablonu). Správce prostředků také vyžaduje, abyste nahráli propojené šablony do účtu úložiště, aby k nim Azure během nasazení mohl přistupovat. Další informace najdete v tématu [nasazení propojených správce prostředků šablon pomocí VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Nezapomeňte přidat skripty Data Factory do kanálu CI/CD před a po úloze nasazení.

Pokud nemáte nakonfigurovaný Git, můžete k propojeným šablonám přistupovat pomocí **šablony pro export ARM** v seznamu **šablon ARM** .

## <a name="hotfix-production-environment"></a>Produkční prostředí s opravou hotfix

Pokud nasadíte továrnu do produkčního prostředí a zjistíte chybu, kterou je potřeba opravit hned, ale nemůžete nasadit aktuální větev spolupráce, možná budete muset nasadit opravu hotfix. Tento přístup je známý jako rychlý opravový technický nebo QFE.

1.    Ve službě Azure DevOps si Projděte verzi, která byla nasazena do produkčního prostředí. Vyhledá poslední přivedený zápis.

2.    Z zprávy potvrzení Získejte ID potvrzení větve pro spolupráci.

3.    Vytvořte novou větev hotfix z tohoto potvrzení.

4.    Přejděte do Azure Data Factory uživatelského rozhraní a přepněte do větve hotfix.

5.    Pomocí Azure Data Factoryho uživatelského rozhraní opravíte chybu. Otestujte provedené změny.

6.    Po ověření opravy vyberte **Exportovat šablonu ARM** , abyste získali Správce prostředků šablonu oprav hotfix.

7.    Toto sestavení ručně zkontrolujete do větve adf_publish.

8.    Pokud jste nakonfigurovali svůj kanál pro vydávání verzí tak, aby se automaticky spouštěl na základě adf_publish vrácení se změnami, automaticky se spustí nová verze. V opačném případě ručně vyřadí vydanou verzi.

9.    Nasaďte verzi opravy hotfix do testovacího a produkčního továrny. Tato verze obsahuje předchozí provozní datovou část a opravu, kterou jste provedli v kroku 5.

10.   Přidejte změny z opravy hotfix do vývojové větve, aby pozdější verze neobsahovaly stejnou chybu.

## <a name="best-practices-for-cicd"></a>Osvědčené postupy pro CI/CD

Pokud používáte integraci Git s datovou továrnou a máte kanál CI/CD, který přesouvá vaše změny z vývoje do testu a následně do produkčního prostředí, doporučujeme tyto osvědčené postupy:

-   **Integrace Gitu**. Nakonfigurujte jenom váš vývojový objekt pro vytváření dat s integrací Gitu. Změny v testovacích a produkčních prostředích se nasazují přes CI/CD a nepotřebují integraci Git.

-   **Skript spouštěný předem a po nasazení**. Před krokem nasazení Správce prostředků v CI/CD je potřeba provést určité úlohy, jako je zastavení a restartování triggerů a provádění čištění. Doporučujeme používat skripty prostředí PowerShell před a po úloze nasazení. Další informace najdete v tématu [aktualizace aktivních aktivačních událostí](#updating-active-triggers). Tým Data Factory zadal [skript](#script) , který se má použít v dolní části této stránky.

-   **Prostředí Integration runtime a sdílení**. Prostředí Integration runtime se často nemění a jsou ve všech fázích CI/CD stejné. Takže Data Factory očekává, že budete mít stejný název a typ prostředí Integration runtime ve všech fázích CI/CD. Pokud chcete sdílet prostředí Integration runtime ve všech fázích, zvažte použití Ternární továrny jenom k zahrnutí sdílených prostředí Integration runtime. Tuto sdílenou továrnu můžete použít ve všech prostředích jako typ propojeného prostředí Integration runtime.

-   **Nasazení spravovaného privátního koncového bodu**. Pokud v továrně již existuje privátní koncový bod a pokusíte se nasadit šablonu ARM, která obsahuje privátní koncový bod se stejným názvem, ale se změněnými vlastnostmi, nasazení se nezdaří. Jinými slovy, můžete úspěšně nasadit soukromý koncový bod, pokud má stejné vlastnosti jako ten, který už v továrně existuje. Pokud je libovolná vlastnost odlišná mezi prostředími, můžete ji přepsat tím, že Parametrizace tuto vlastnost a při nasazení poskytnete příslušnou hodnotu.

-   **Key Vault**. Při použití propojených služeb, jejichž informace o připojení jsou uloženy v Azure Key Vault, se doporučuje uchovávat samostatné trezory klíčů pro různá prostředí. Pro každý Trezor klíčů můžete nakonfigurovat také samostatné úrovně oprávnění. Například nebudete chtít, aby členové týmu měli oprávnění k produkčním tajným klíčům. Pokud budete postupovat podle tohoto přístupu, doporučujeme, abyste zachovali stejné tajné názvy ve všech fázích. Pokud zachováte stejné tajné názvy, nemusíte v prostředích CI/CD parametrizovat jednotlivé připojovací řetězce, protože jediná změna je název trezoru klíčů, což je samostatný parametr.

-  **Pojmenovávání prostředků** Kvůli omezením šablony ARM můžou problémy v nasazení nastat, pokud vaše prostředky v názvu obsahují mezery. Tým Azure Data Factory doporučuje místo mezer pro prostředky používat znaky _ nebo-. Například ' Pipeline_1 ' by byl upřednostňovaným názvem přes ' kanál 1 '.

## <a name="unsupported-features"></a>Nepodporované funkce

- Data Factory tak, že neumožňuje vybírání položek potvrzení nebo selektivního publikování prostředků. Publikování budou zahrnovat všechny změny provedené v datové továrně.

    - Entity Data Factory jsou vzájemně závislé. Například triggery závisejí na kanálech a kanály závisí na datových sadách a dalších kanálech. Selektivní publikování podmnožiny prostředků by mohlo vést k neočekávanému chování a chybám.
    - Ve výjimečných případech když potřebujete selektivní publikování, zvažte použití opravy hotfix. Další informace najdete v tématu [provozní prostředí oprav hotfix](#hotfix-production-environment).

- Tým Azure Data Factory nedoporučuje přiřazovat řízení RBAC jednotlivým entitám (kanálům, datovým sadám atd.) v datové továrně. Pokud má například vývojář přístup k kanálu nebo datové sadě, měl by mít přístup ke všem kanálům nebo datovým sadám v datové továrně. Pokud se domníváte, že v rámci datové továrny potřebujete implementovat mnoho rolí RBAC, podívejte se na nasazení druhého objektu pro vytváření dat.

-   Nemůžete publikovat z privátních větví.

-   V současné době nemůžete hostovat projekty v Bitbucket.

## <a name="sample-pre--and-post-deployment-script"></a><a name="script"></a> Ukázka skriptu předběžného a po nasazení

Pomocí následujícího ukázkového skriptu můžete před nasazením zastavit triggery a potom je znovu restartovat. Skript také obsahuje kód pro odstranění odebraných prostředků. Uložte skript do úložiště Git Azure DevOps a prokažte ho pomocí úlohy Azure PowerShell s použitím verze 4. *.

Při spuštění skriptu předběžného nasazení bude nutné zadat variaci následujících parametrů v poli **argumenty skriptu** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Při spuštění skriptu po nasazení bude nutné zadat variaci následujících parametrů v poli **argumenty skriptu** .

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell úkol](media/continuous-integration-deployment/continuous-integration-image11.png)

Tady je skript, který se dá použít k předběžnému a následnému nasazení. Účty IT pro odstraněné prostředky a odkazy na prostředky.

  
```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    if ($trigger.Properties.DependsOn) {
        $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
            triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
        }
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersInTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNamesInTemplate = $triggersInTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}

$triggersDeployed = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

$triggersToStop = $triggersDeployed | Where-Object { $triggerNamesInTemplate -contains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToDelete = $triggersDeployed | Where-Object { $triggerNamesInTemplate -notcontains $_.Name } | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.Name
        TriggerType = $_.Properties.GetType().Name 
    }
}
$triggersToStart = $triggersInTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object { 
    New-Object PSObject -Property @{
        Name = $_.name.Substring(37, $_.name.Length-40)
        TriggerType = $_.Properties.type
    }
}

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers`n"
    $triggersToStop | ForEach-Object {
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Unsubscribing" $_.Name "from events"
            $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Disabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Stopping trigger" $_.Name
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $triggersToDelete | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            if ($_.TriggerType -eq "BlobEventsTrigger") {
                Write-Host "Unsubscribing trigger" $_.Name "from events"
                $status = Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                while ($status.Status -ne "Disabled"){
                    Start-Sleep -s 15
                    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
                }
            }
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $triggersToStart | ForEach-Object { 
        if ($_.TriggerType -eq "BlobEventsTrigger") {
            Write-Host "Subscribing" $_.Name "to events"
            $status = Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            while ($status.Status -ne "Enabled"){
                Start-Sleep -s 15
                $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name
            }
        }
        Write-Host "Starting trigger" $_.Name
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force
    }
}
```
