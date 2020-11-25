---
title: Webhooky, které reagují na akce registru
description: Naučte se, jak pomocí webhooků aktivovat události, když se akce push nebo Pull vyskytnou v úložištích registru.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 1db1098da81e6cf9ecb262c99f705b77af2efd26
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004479"
---
# <a name="using-azure-container-registry-webhooks"></a>Používání Azure Container Registry webhooků

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů Dockeru podobným způsobem, jakým Docker Hub uchovává veřejné image Dockeru. Může také hostovat úložiště pro [grafy Helm](container-registry-helm-repos.md) (Preview), formát balení pro nasazení aplikací do Kubernetes. Webhooky můžete použít k aktivaci událostí, když probíhají určité akce v jednom z úložišť registru. Webhooky můžou reagovat na události na úrovni registru nebo můžou být vymezené na konkrétní značku úložiště. Pomocí  [geograficky replikovaného](container-registry-geo-replication.md) registru můžete každý Webhook nakonfigurovat tak, aby reagoval na události v konkrétní místní replice.

Podrobnosti o požadavcích Webhooku najdete v tématu [Azure Container Registry Reference k schématu Webhooku](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Požadavky

* Registr kontejnerů Azure – Vytvořte registr kontejnerů ve svém předplatném Azure. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md). [Úrovně služby Azure Container Registry](container-registry-skus.md) mají různé kvóty webhooků.
* Rozhraní příkazového řádku Dockeru – Chcete-li nastavit místní počítač jako hostitele Dockeru a získat přístup k příkazům rozhraní příkazového řádku Dockeru, nainstalujte si modul runtime [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Vytvořit Webhook – Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte do registru kontejneru, ve kterém chcete vytvořit Webhook.
1. V části **služby** vyberte **Webhooky**.
1. Na panelu nástrojů Webhooku vyberte **Přidat** .
1. Vyplňte formulář *vytvořit Webhook* s následujícími informacemi:

| Hodnota | Popis |
|---|---|
| Název Webhooku | Název, který chcete předat Webhooku. Může obsahovat jenom písmena a číslice a musí mít délku 5-50 znaků. |
| Umístění | V případě [geograficky replikovaného](container-registry-geo-replication.md) registru zadejte oblast Azure repliky registru. 
| Identifikátor URI služby | Identifikátor URI, kam má Webhook odeslat oznámení POST |
| Vlastní hlavičky | Hlavičky, které chcete předat spolu s požadavkem POST. Měly by být ve formátu "klíč: hodnota". |
| Aktivační akce | Akce, které aktivují Webhook Akce zahrnují vložení obrázku, odstranění obrázku, vložení grafu Helm, odstranění grafu Helm a karanténu obrázku. Můžete vybrat jednu nebo více akcí pro aktivaci Webhooku. |
| Status | Stav Webhooku po jeho vytvoření. Tato možnost je ve výchozím nastavení povolená. |
| Rozsah | Rozsah, ve kterém Webhook pracuje. Pokud tento parametr nezadáte, bude obor pro všechny události v registru. Dá se zadat pro úložiště nebo značku pomocí formátu "úložiště: Značka" nebo "úložiště: *" pro všechny značky v úložišti. |

Příklad formuláře Webhooku:

![Snímek obrazovky, který ukazuje vytvoření Webhooku ACR U I v Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Vytvoření Webhooku – Azure CLI

Pokud chcete vytvořit Webhook pomocí Azure CLI, použijte příkaz [AZ ACR Webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) . Následující příkaz vytvoří Webhook pro všechny události odstranění imagí v registru *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Test Webhooku

### <a name="azure-portal"></a>portál Azure

Před použitím Webhooku ho můžete otestovat pomocí tlačítka **otestovat** . Nástroj test odesílá do zadaného koncového bodu obecný požadavek POST a zaznamená odpověď. Použití funkce příkazem testovat v nástroji můžete ověřit, že jste Webhook správně nakonfigurovali.

1. Vyberte Webhook, který chcete testovat.
2. Na horním panelu nástrojů vyberte **příkaz Testovat**.
3. Ve sloupci **stav HTTP** ověřte odpověď koncového bodu.

![ACR uživatelského rozhraní pro vytvoření Webhooku v Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Pokud chcete otestovat Webhook ACR pomocí Azure CLI, použijte příkaz [AZ ACR Webhook příkazu příkazového](/cli/azure/acr/webhook#az-acr-webhook-ping) řádku.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Výsledky zobrazíte pomocí příkazu [AZ ACR Webhook list-Events](/cli/azure/acr/webhook) .

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Odstranit Webhook

### <a name="azure-portal"></a>portál Azure

Každý Webhook se dá odstranit tak, že vyberete Webhook a pak na Azure Portal tlačítko **Odstranit** .

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Další kroky

### <a name="webhook-schema-reference"></a>Referenční dokumentace schématu Webhooku

Podrobnosti o formátu a vlastnostech datových částí události JSON vygenerovaných Azure Container Registry najdete v referenčních informacích o schématu Webhooku:

[Odkaz na schéma Azure Container Registry Webhooku](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Události Event Grid

Kromě událostí Webhooku v nativním registru, které jsou popsány v tomto článku, Azure Container Registry mohou generovat události Event Grid:

[Rychlý Start: odeslání událostí registru kontejneru do Event Grid](container-registry-event-grid-quickstart.md)
