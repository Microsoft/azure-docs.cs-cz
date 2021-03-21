---
title: Vytváření, zobrazování a Správa systémových témat Azure Event Grid pomocí rozhraní příkazového řádku
description: Tento článek popisuje, jak pomocí Azure CLI vytvářet, zobrazovat a odstraňovat systémová témata.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633218"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Vytváření, zobrazování a Správa systémových témat Event Grid pomocí Azure CLI
V tomto článku se dozvíte, jak vytvořit a spravovat systémová témata pomocí Azure CLI. Přehled systémových témat najdete v tématu [Systémová témata](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Nainstalovat rozšíření pro Azure CLI
V případě Azure CLI potřebujete [rozšíření Event Grid](/cli/azure/azure-cli-extensions-list).

V Cloud Shell:

- Pokud jste dříve nainstalovali rozšíření, aktualizujte ho: `az extension update -n eventgrid`
- Pokud jste rozšíření dříve neinstalovali, nainstalujte ho:  `az extension add -n eventgrid`

Pro místní instalaci:

1. [Nainstalujte rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Ujistěte se, že máte nejnovější verzi, pomocí kontroly `az --version` .
2. Odinstalujte předchozí verze rozšíření: `az extension remove -n eventgrid`
3. Nainstalujte rozšíření eventgrid s `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Vytvořit systémové téma

- Chcete-li nejprve vytvořit systémové téma na zdroji Azure a pak vytvořit odběr události pro toto téma, přečtěte si následující referenční témata:
    - [AZ eventgrid System-téma Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        Seznam `topic-type` hodnot, které můžete použít k vytvoření systémového tématu, získáte spuštěním následujícího příkazu. Tyto hodnoty typů tématu reprezentují zdroje událostí, které podporují vytváření systémových témat. Seznam prosím ignorujte `Microsoft.EventGrid.Topics` a `Microsoft.EventGrid.Domains` ze seznamu. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [AZ eventgrid System-téma Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Pokud chcete vytvořit systémové téma (implicitně) při vytváření odběru událostí pro zdroj Azure, použijte metodu [AZ eventgrid Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) . Tady je příklad:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Kurz s podrobnými pokyny najdete v tématu [přihlášení k odběru účtu úložiště](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Zobrazit všechna systémová témata
Chcete-li zobrazit všechna systémová témata a podrobnosti o vybraném systémovém tématu, použijte následující příkazy:

- [AZ eventgrid System – seznam témat](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System-téma show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Odstranit systémové téma
Chcete-li odstranit systémové téma, použijte následující příkaz: 

- [AZ eventgrid System-téma Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Další kroky
V části [Systémová témata v Azure Event Grid](system-topics.md) najdete další informace o systémových tématech a typech témat, které podporuje Azure Event Grid. 