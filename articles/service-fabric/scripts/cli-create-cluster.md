---
title: Ukázka nasazení skriptu Azure CLI
description: Vytvořte zabezpečený cluster Service Fabric Linux v Azure s použitím rozhraní příkazového řádku Azure CLI.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 29313c3b3dc7b140b566cb4d7a52406fc45fe0f0
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695512"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Vytvoření zabezpečeného clusteru Service Fabric Linux v Azure

Tento příkaz vytvoří certifikát podepsaný svým držitelem (self-signed certificate), přidá ho do trezoru klíčů a stáhne certifikát místně.  Nový certifikát se použije k zabezpečení clusteru při nasazování.  Nemusíte vytvářet nový certifikát, místo toho můžete použít certifikát stávající.  V obou případech musí název subjektu certifikátu odpovídat doméně, kterou používáte pro přístup ke clusteru Service Fabric. Tato shoda se vyžaduje kvůli zajištění zabezpečení SSL pro koncové body správy prostřednictvím protokolu HTTPS a pro Service Fabric Explorer clusteru. Certifikát SSL nelze pro doménu `.cloudapp.azure.com` získat od certifikační autority. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

V případě potřeby nainstalujte [Azure CLI](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, cluster a všechny související prostředky.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Vytvoří nový cluster Service Fabric.  |

## <a name="next-steps"></a>Další postup

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
