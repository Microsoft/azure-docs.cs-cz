---
title: Ukázkový skript Azure CLI – vytvoření aplikace logiky
description: Ukázkový skript pro vytvoření aplikace logiky prostřednictvím rozšíření Logic Apps v rozhraní příkazového řádku Azure CLI.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: b81d9b4a637965dd103d8fa89305424686a0c72c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789910"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Ukázkový skript Azure CLI – vytvoření aplikace logiky

Tento skript vytvoří ukázkovou aplikaci logiky prostřednictvím [rozšíření Azure CLI Logic Apps](/cli/azure/ext/logic/logic)( `az logic` ). Podrobný průvodce vytvářením a správou Logic Apps prostřednictvím rozhraní příkazového řádku Azure najdete v tématu [rychlý start Logic Apps pro rozhraní](quickstart-logic-apps-azure-cli.md)příkazového řádku Azure CLI.

> [!WARNING]
> Rozšíření Azure CLI Logic Apps v současné době *experimentální* a nezabývá se *zákaznickou podporou*. Toto rozšíření CLI používejte opatrně, zejména pokud se rozhodnete použít rozšíření v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. Pokud nemáte předplatné Azure, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nainstalované na místním počítači.
* V počítači je nainstalovaná [Logic Apps rozšíření Azure CLI](/cli/azure/azure-cli-extensions-list) . Chcete-li nainstalovat toto rozšíření, použijte tento příkaz: `az extension add --name logic`
* [Definice pracovního postupu](quickstart-logic-apps-azure-cli.md#workflow-definition) pro aplikaci logiky Tento soubor JSON musí splňovat [schéma jazyka definice pracovního postupu](logic-apps-workflow-definition-language.md).
* Připojení rozhraní API k e-mailovému účtu prostřednictvím podporovaného [konektoru Logic Apps](../connectors/apis-list.md) ve stejné skupině prostředků jako vaše aplikace logiky. V tomto příkladu se používá konektor [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) , ale můžete použít i jiné konektory, jako je [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Než začnete, ověřte si prostředí:

* Přihlaste se k Azure Portal a ověřte, že je vaše předplatné aktivní spuštěním `az login` .

* Podívejte se na verzi rozhraní příkazového řádku Azure CLI v terminálu nebo příkazovém okně spuštěním příkazu `az --version` . Nejnovější verzi najdete v [poznámkách k nejnovější verzi](/cli/azure/release-notes-azure-cli).

  * Pokud nemáte nejnovější verzi, aktualizujte instalaci pomocí [instalační příručky pro váš operační systém nebo platformu](/cli/azure/install-azure-cli).

### <a name="sample-workflow-explanation"></a>Vysvětlení ukázkového pracovního postupu

Tento ukázkový soubor definice pracovního postupu vytvoří stejnou základní aplikaci logiky jako [Logic Apps rychlý Start pro Azure Portal](quickstart-create-first-logic-app-workflow.md). 

Tento ukázkový pracovní postup: 

1. Určuje schéma, `$schema` pro aplikaci logiky.

1. Definuje Trigger aplikace logiky v seznamu aktivačních událostí `triggers` . Aktivační událost se opakuje ( `recurrence` ) každé 3 hodiny. Akce se aktivují při publikování nové položky informačního kanálu ( `When_a_feed_item_is_published` ) pro zadaný informační kanál RSS ( `feedUrl` ).

1. Definuje akci pro aplikaci logiky v seznamu akcí `actions` . Akce pošle e-mail ( `Send_an_email_(V2)` ) prostřednictvím Microsoft 365 s podrobnostmi z položek informačního kanálu RSS, jak je uvedeno v části tělo () `body` vstupů akce ( `inputs` ).

## <a name="sample-workflow-definition"></a>Ukázková definice pracovního postupu

Před spuštěním ukázkového skriptu je nutné nejprve vytvořit ukázkovou [definici pracovního postupu](#prerequisites).

1. Vytvořte `testDefinition.json` ve svém počítači soubor JSON. 

1. Zkopírujte následující obsah do souboru JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Aktualizujte zástupné hodnoty vlastními informacemi:

    1. Nahraďte zástupnou e-mailovou adresu ( `"To": "test@example.com"` ). Je nutné použít e-mailovou adresu kompatibilní s Logic Apps konektory. Další informace najdete v části [požadavky](#prerequisites).

    1. Pokud používáte jiný e-mailový konektor než konektor Office 365 Outlook, nahraďte další podrobnosti konektoru.

    1. Nahraďte zástupný symbol hodnoty ( `00000000-0000-0000-0000-000000000000` ) pro identifikátory připojení ( `connectionId` a `id` ) v parametru připojení ( `$connections` ) s vlastními hodnotami předplatného.

1. Uložte provedené změny.

## <a name="sample-script"></a>Ukázkový skript

> [!NOTE]
> Tato ukázka je zapsána pro `bash` prostředí. Pokud chcete tuto ukázku spustit v jiném prostředí, například v prostředí Windows PowerShell nebo příkazovém řádku, může být nutné provést úpravy skriptu.

Než spustíte tento ukázkový skript, spusťte tento příkaz pro připojení k Azure:

```azurecli-interactive

az login

```

Potom přejděte do adresáře, ve kterém jste vytvořili definici pracovního postupu. Pokud jste například vytvořili soubor JSON definice pracovního postupu na ploše:

```azurecli

cd ~/Desktop

```

Potom spuštěním tohoto skriptu vytvořte aplikaci logiky. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Po dokončení používání ukázkového skriptu spusťte následující příkaz, který odebere skupinu prostředků a všechny její vnořené prostředky, včetně aplikace logiky.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento ukázkový skript používá následující příkazy k vytvoření nové skupiny prostředků a aplikace logiky.

| Příkaz | Poznámky |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které jsou uložené prostředky aplikace logiky. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Vytvoří aplikaci logiky na základě pracovního postupu definovaného v parametru `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Odstraní skupinu prostředků a všechny její vnořené prostředky. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v dokumentaci k rozhraní příkazového [řádku Azure CLI](/cli/azure/).

Logic Apps Další ukázkové skripty rozhraní příkazového řádku najdete v [prohlížeči ukázek kódu Microsoftu](/samples/browse/?products=azure-logic-apps).
