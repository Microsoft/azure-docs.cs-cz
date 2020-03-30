---
title: Webhooky pro reakci na akce registru
description: Naučte se používat webhooky k aktivaci událostí, když dojde k akcím push nebo pull v repozitářích registru.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454377"
---
# <a name="using-azure-container-registry-webhooks"></a>Použití webových háků registru kontejnerů Azure

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů Dockeru podobným způsobem, jakým Docker Hub uchovává veřejné image Dockeru. Může také hostovat repozitáře pro [grafy Helm](container-registry-helm-repos.md) (preview), formát balení pro nasazení aplikací do Kubernetes. Pomocí webhooků můžete aktivovat události, když se určité akce uskuteční v jednom z úložišť registru. Webhooky mohou reagovat na události na úrovni registru nebo mohou být vymezeny na konkrétní značku úložiště. Pomocí [geograficky replikovaného](container-registry-geo-replication.md) registru nakonfigurujete každý webhook tak, aby reagoval na události v konkrétní místní replice.

Podrobnosti o požadavcích na webhooku najdete v [tématu Azure Container Registry webhook](container-registry-webhook-reference.md)odkaz na schéma .

## <a name="prerequisites"></a>Požadavky

* Registr kontejnerů Azure – Vytvořte registr kontejnerů ve svém předplatném Azure. Například použijte [portál Azure](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md). [Virtuální počítače registru azure](container-registry-skus.md) registru mají různé kvóty webhooky.
* Rozhraní příkazového řádku Dockeru – Chcete-li nastavit místní počítač jako hostitele Dockeru a získat přístup k příkazům rozhraní příkazového řádku Dockeru, nainstalujte si modul runtime [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Vytvořit webhook – portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte do registru kontejneru, ve kterém chcete vytvořit webhooku.
1. V části **Služby**vyberte **možnost Webhooks**.
1. Vyberte **Přidat** na panelu nástrojů webhooku.
1. Vyplňte formulář *Vytvořit webhook* u následujících informací:

| Hodnota | Popis |
|---|---|
| Název webhooku | Název, který chcete dát webhooku. Může obsahovat pouze písmena a číslice a musí mít délku 5-50 znaků. |
| Umístění | Pro [geograficky replikovaný](container-registry-geo-replication.md) registr zadejte oblast Azure repliky registru. 
| Identifikátor URI služby | Identifikátor URI, kde webhooku by měl odeslat oznámení POST. |
| Vlastní hlavičky | Záhlaví, které chcete předat spolu s požadavkem POST. Měly by být ve formátu "klíč: hodnota". |
| Spouštěcí akce | Akce, které aktivují webhooku. Akce zahrnují nabízení obrázků, odstranění obrázku, nabízení grafu Helm, odstranění helmového grafu a karanténu obrázků. Můžete zvolit jednu nebo více akcí pro aktivaci webového háčku. |
| Status | Stav webhooku po jeho vytvoření. Ve výchozím nastavení je povolena. |
| Rozsah | Obor, ve kterém webhooku funguje. Pokud není zadán, obor je pro všechny události v registru. Lze jej zadat pro úložiště nebo značku pomocí formátu "repository:tag" nebo "repository:*" pro všechny značky v úložišti. |

Příklad formuláře webhooku:

![Vytvoření uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského místa pro webhook u ACR na webu Azure Portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Vytvoření webhooku – azure cli

Chcete-li vytvořit webhooku pomocí příkazového příkazu Azure CLI, použijte příkaz [az acr webhook create.](/cli/azure/acr/webhook#az-acr-webhook-create) Následující příkaz vytvoří webhooku pro všechny události odstranění bitové kopie v registru *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Test webhooku

### <a name="azure-portal"></a>portál Azure

Před použitím webhooku jej můžete otestovat pomocí tlačítka **Ping.** Příkaz Ping odešle obecný požadavek POST zadanému koncovému bodu a zaznamená odpověď. Pomocí funkce ping vám může pomoci ověřit, že jste správně nakonfigurovali webhook.

1. Vyberte webhook, který chcete testovat.
2. V horním panelu nástrojů vyberte **příkaz Ping**.
3. Zkontrolujte odpověď koncového bodu ve sloupci **STAV HTTP.**

![Vytvoření uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského místa pro webhook u ACR na webu Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Chcete-li otestovat webhook ACR s Azure CLI, použijte příkaz [ping az acr webhook.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Chcete-li zobrazit výsledky, použijte [az acr webhook seznam událostí](/cli/azure/acr/webhook) příkazu.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Odstranit webový hák

### <a name="azure-portal"></a>portál Azure

Každý webhooku se dá odstranit výběrem webhooku a potom tlačítkem **Delete** na webu Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Další kroky

### <a name="webhook-schema-reference"></a>Webhook odkaz na schéma

Podrobnosti o formátu a vlastnostech datové části událostí JSON vyzařované registrem kontejnerů Azure najdete v tématu odkaz na schéma webhooku:

[Odkaz na webhookové schéma registru azure kontejnerů](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Události v mřížce událostí

Kromě nativních událostí webhooku registru popsaných v tomto článku může Registr kontejnerů Azure vyzařovat události do mřížky událostí:

[Úvodní příručka: Odeslání událostí registru kontejneru do mřížky událostí](container-registry-event-grid-quickstart.md)
