---
title: Azure webhooky registru kontejneru
description: Další informace o použití webhooky aktivační události, pokud dojde k určité akce ve vaší registru úložiště.
services: container-registry
author: iainfoulds
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: iainfou
ms.openlocfilehash: 538548fa211b2159b190ff1b689033c50ad801fe
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096304"
---
# <a name="using-azure-container-registry-webhooks"></a>Pomocí webhooků registru kontejner Azure

Azure kontejneru registru uchovává a spravuje privátní kontejner imagí Dockeru, podobně jako úložiště Docker Hub ukládá veřejné imagí Dockeru. Můžete použít webhooky aktivační události při určité akce provést v jednom z vašeho úložiště registru. Webhooky můžete reakce na události na úrovni registru, nebo může být snížit značku konkrétní úložiště.

Podrobnosti o žádostech webhooku najdete v tématu [registru kontejner Azure webhooku schéma – referenční informace](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Požadavky

* Kontejner Azure registru - vytvořit kontejner registru ve vašem předplatném Azure. Například použít [portál Azure](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
* Instalace rozhraní příkazového řádku - nastavení místního počítače jako hostitele Docker a přístup k příkazy příkazového řádku Dockeru docker [modulu Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Vytvoření webhooku portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com)
1. Přejděte do kontejneru registru, ve kterém chcete vytvořit webhook, jehož.
1. V části **služby**, vyberte **Webhooky**.
1. Vyberte **přidat** na panelu nástrojů webhooku.
1. Dokončení *vytvoření webhooku* formuláře s následujícími informacemi:

| Hodnota | Popis |
|---|---|
| Název | Název, který chcete poskytnout k webhooku. Může obsahovat jenom malá písmena a čísla a musí být 5 – 50 znaků. |
| Identifikátor URI služby | Identifikátor URI, kde má webhooku odeslat oznámení POST. |
| Vlastní záhlaví | Hlavičky, kterou chcete předat společně s požadavek POST. Musí být v "klíč: hodnota" formátu. |
| Akce aktivace | Akce, které aktivují webhooku. Webhooky mohou být aktivovány nabízené bitové kopie nebo odstranění akcí. |
| Status | Stav webhooku po jejím vytvoření. Ve výchozím nastavení je povolená. |
| Rozsah | Obor, ve kterém funguje webhooku. Ve výchozím nastavení rozsah je pro všechny události v registru. Můžete zadat pro úložiště nebo značku ve formátu "úložiště: značka". |

Příklad webhooku formuláře:

![Vytvoření webhooku ACR uživatelského rozhraní na portálu Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Vytvoření webhooku rozhraní příkazového řádku Azure

Chcete-li vytvořit webhook, jehož pomocí rozhraní příkazového řádku Azure, použijte [vytvoření webhooku acr az](/cli/azure/acr/webhook#az_acr_webhook_create) příkaz.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Test webhooku

### <a name="azure-portal"></a>Azure Portal

Před použitím webhooku na kontejneru bitové kopie nabízené a odstranění akcí, můžete otestovat ji s **Ping** tlačítko. Příkaz ping odešle požadavek POST obecné zadaný koncový bod a protokoly odpovědi. Pomocí příkazu ping funkce můžete ověřit, zda že jste správně nakonfigurovali webhooku.

1. Vyberte webhooku, kterou chcete otestovat.
2. V horním panelu nástrojů vyberte **Ping**.
3. Zkontrolujte v odpovědi pro koncový bod **stav protokolu HTTP** sloupce.

![Vytvoření webhooku ACR uživatelského rozhraní na portálu Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Chcete-li otestovat webhook ACR pomocí Azure CLI, použijte [az acr webhooku ping](/cli/azure/acr/webhook#az_acr_webhook_ping) příkaz.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Pokud chcete zobrazit výsledky, použijte [az acr webhooku seznam události](/cli/azure/acr/webhook#list-events) příkaz.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Odstranit webhook

### <a name="azure-portal"></a>Azure Portal

Každý webhooku odstraníte výběrem webhooku a potom **odstranit** tlačítka na portálu Azure.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Další postup

[Referenční dokumentace schématu webhooku registru kontejner Azure](container-registry-webhook-reference.md)