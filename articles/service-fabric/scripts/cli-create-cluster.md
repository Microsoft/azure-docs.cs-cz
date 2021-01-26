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
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 675f6b9ac3ebb05539432b5febc279275f9de7f6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785765"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Vytvoření zabezpečeného clusteru Service Fabric Linux v Azure

Tento příkaz vytvoří certifikát podepsaný svým držitelem (self-signed certificate), přidá ho do trezoru klíčů a stáhne certifikát místně.  Nový certifikát se použije k zabezpečení clusteru při nasazování.  Nemusíte vytvářet nový certifikát, místo toho můžete použít certifikát stávající.  V obou případech musí název subjektu certifikátu odpovídat doméně, kterou používáte pro přístup ke clusteru Service Fabric. Tato shoda se vyžaduje k poskytnutí TLS pro koncové body správy HTTPS clusteru a Service Fabric Explorer. Certifikát TLS/SSL nemůžete od certifikační autority pro `.cloudapp.azure.com` doménu získat. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

V případě potřeby nainstalujte [Azure CLI](/en-us/cli/azure/install-azure-cli).

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
| [az sf cluster create](/cli/azure/sf/cluster) | Vytvoří nový cluster Service Fabric.  |

## <a name="next-steps"></a>Další kroky

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
