---
title: Azure Container Registry webhooků
description: Další informace o použití webhooky k aktivační události, když dojde k určité akce v úložištích registru.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/20/2017
ms.author: marsma
ms.openlocfilehash: c424e81b13c3c60e975d3721693b1f80e00cfdd7
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059111"
---
# <a name="using-azure-container-registry-webhooks"></a>Použití webhooků v Azure Container Registry

Registr kontejnerů Azure uchovává a spravuje privátní Image kontejnerů Dockeru, podobným způsobem, jakým Docker Hub uchovává veřejné Image Dockeru. Webhooky k aktivační události při určité akce proběhla v jednom z vašich úložišť registru můžete použít. Webhooky můžou reagovat na události na úrovni registru nebo může být oboru dolů značku konkrétního úložiště.

Podrobnosti o požadavky webhooku, najdete v části [referenční dokumentace schématu Azure Container Registry webhooku](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Požadavky

* Azure container registry – vytvoření registru kontejnerů ve vašem předplatném Azure. Například použít [webu Azure portal](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* Instalace rozhraní příkazového řádku – nastavení místního počítače jako hostitele Docker a přístup k příkazům rozhraní příkazového řádku Dockeru, dockeru [modul Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Vytvořit webhook webu Azure portal

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte do registru kontejneru, ve kterém chcete vytvořit webhook.
1. V části **služby**vyberte **Webhooky**.
1. Vyberte **přidat** na panelu nástrojů webhooku.
1. Dokončení *vytvořit webhook* formuláře následující informace:

| Hodnota | Popis |
|---|---|
| Název | Jméno, které chcete přidělit k webhooku. Může obsahovat jenom malá písmena a číslice a musí mít délku 5 až 50 znaků. |
| Identifikátor URI služby | Identifikátor URI, kde by měl webhook odesílání oznámení příspěvku. |
| Vlastní hlavičky | Hlavičky, které chcete předat spolu s požadavek POST. Uživatelé by měli být "klíč: hodnota" formátu. |
| Aktivační událost akce | Akce, které aktivují webhook. Webhooky můžou být aktivované nabízených image a/nebo odstranit některé akce. |
| Status | Stav pro webhook po jeho vytvoření. Ve výchozím nastavení je povoleno. |
| Rozsah | Rozsah, kdy se webhook funguje. Ve výchozím nastavení je oboru pro všechny události v registru. To se dá nastavit pro úložiště nebo značku ve formátu "úložiště: značka". |

Ukázkový formulář webhooku:

![ACR webhook vytváření uživatelského rozhraní na webu Azure Portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Vytvořit webhook, rozhraní příkazového řádku Azure

Chcete-li vytvořit webhook pomocí Azure CLI, použijte [az acr webhook vytvořit](/cli/azure/acr/webhook#az-acr-webhook-create) příkazu.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testovat webhook

### <a name="azure-portal"></a>portál Azure

Předchozí pomocí webhooku v kontejneru obrázků nabízených oznámení a akce odstranění, můžete ho otestovat **Ping** tlačítko. Příkaz ping odešle obecný požadavek POST na zadaný koncový bod a protokoly odpovědi. Pomocí příkazu ping funkce můžete ověřit, že jste správně nakonfigurovali webhooku.

1. Vyberte, kterou chcete otestovat webhook.
2. V horním panelu nástrojů vyberte **Ping**.
3. Vrátit se změnami koncového odpovědi **stav protokolu HTTP** sloupce.

![ACR webhook vytváření uživatelského rozhraní na webu Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Chcete-li otestovat webhook služby ACR pomocí Azure CLI, použijte [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) příkazu.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Chcete-li zobrazit výsledky, použijte [az acr události seznamu webhooku-](/cli/azure/acr/webhook#list-events) příkazu.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Odstranit webhook

### <a name="azure-portal"></a>portál Azure

Každý webhook můžete odstranit tak, že vyberete webhooku a pak **odstranit** tlačítko na webu Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Další postup

### <a name="webhook-schema-reference"></a>Odkaz na schéma Webhooku

Podrobnosti o formátu a vlastnostech datové části události JSON, protože ho vygeneroval Azure Container Registry najdete v tématu referenční dokumentace schématu webhooku:

[Referenční dokumentace schématu webhook registru kontejneru Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Událostí služby Event Grid

Kromě události webhooku nativní registru popsané v tomto článku může Azure Container Registry vysílat události do služby Event Grid:

[Rychlý start: Odeslání kontejneru událostí registru do služby Event Grid](container-registry-event-grid-quickstart.md)