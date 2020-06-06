---
title: Vytváření, zobrazování a Správa systémových témat Azure Event Grid pomocí rozhraní příkazového řádku
description: Tento článek popisuje, jak pomocí Azure CLI vytvářet, zobrazovat a odstraňovat systémová témata.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: spelluru
ms.openlocfilehash: c361e7303f73aee1d2e60bd4dd0b9ed0d0e4746f
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457460"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Vytváření, zobrazování a Správa systémových témat Event Grid pomocí Azure CLI
V tomto článku se dozvíte, jak vytvořit a spravovat systémová témata pomocí Azure CLI. Přehled systémových témat najdete v tématu [Systémová témata](system-topics.md).

## <a name="create-a-system-topic"></a>Vytvořit systémové téma

- Chcete-li nejprve vytvořit systémové téma na zdroji Azure a pak vytvořit odběr události pro toto téma, přečtěte si následující referenční témata:
    - [AZ eventgrid System-téma Create](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-create)

        ```azurecli
        az eventgrid system-topic create \
            -g myResourceGroup \
            --name systemtopic1 \
            --location westus2 \
            --topic-type microsoft.storage.storageaccounts \
            --source /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/storagegaccountname
        ```
    - [AZ eventgrid System-téma Event-Subscription Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli
        az eventgrid system-topic event-subscription create --name es1 \
            -g rg1 --system-topic-name systemtopic1 \
            --endpoint https://contoso.azurewebsites.net/api/f1?code=code         
          ```
- To create a system topic (implicitly) when creating an event subscription for an Azure source, use the [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription?view=azure-cli-latest#ext-eventgrid-az-eventgrid-event-subscription-create) method. Here's an example:
    
    ```azurecli
    storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
    endpoint=https://$sitename.azurewebsites.net/api/updates

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <event_subscription_name> \
      --endpoint $endpoint
    ```
    Kurz s podrobnými pokyny najdete v tématu [přihlášení k odběru účtu úložiště](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Zobrazit všechna systémová témata
Chcete-li zobrazit všechna systémová témata a podrobnosti o vybraném systémovém tématu, použijte následující příkazy:

- [AZ eventgrid System – seznam témat](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli
    az eventgrid system-topic list   
     ```
- [AZ eventgrid System-téma show](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli
    az eventgrid system-topic show -g rg1 -n systemtopic1    
     ```

## <a name="delete-a-system-topic"></a>Odstranit systémové téma
Chcete-li odstranit systémové téma, použijte následující příkaz: 

- [AZ eventgrid System-téma Delete](/cli/azure/ext/eventgrid/eventgrid/system-topic?view=azure-cli-latest#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli
    az eventgrid system-topic delete -g myResourceGroup --name systemtopic1  
     ```

## <a name="next-steps"></a>Další kroky
V části [Systémová témata v Azure Event Grid](system-topics.md) najdete další informace o systémových tématech a typech témat, které podporuje Azure Event Grid. 
