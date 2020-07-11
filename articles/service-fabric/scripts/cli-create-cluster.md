---
title: Ukázka nasazení skriptu Azure CLI
description: Jak vytvořit zabezpečený Cluster Service Fabric Linux v Azure pomocí rozhraní příkazového řádku Azure (CLI).
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b454ab7396b8185e344944d7ff526414540032e2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258927"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Vytvoření zabezpečeného clusteru Service Fabric Linux v Azure

Tento příkaz vytvoří certifikát podepsaný svým držitelem (self-signed certificate), přidá ho do trezoru klíčů a stáhne certifikát místně.  Nový certifikát se použije k zabezpečení clusteru při nasazování.  Nemusíte vytvářet nový certifikát, místo toho můžete použít certifikát stávající.  V obou případech musí název subjektu certifikátu odpovídat doméně, kterou používáte pro přístup ke clusteru Service Fabric. Tato shoda se vyžaduje k poskytnutí TLS pro koncové body správy HTTPS clusteru a Service Fabric Explorer. Certifikát TLS/SSL nemůžete od certifikační autority pro `.cloudapp.azure.com` doménu získat. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

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
| [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) | Vytvoří nový cluster Service Fabric.  |

## <a name="next-steps"></a>Další kroky

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
