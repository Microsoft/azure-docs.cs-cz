---
title: Ukázka nasazení skriptu Azure CLI
description: Jak vytvořit zabezpečený cluster Service Fabric Linux v Azure pomocí rozhraní Příkazového řádku Azure (CLI).
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
ms.openlocfilehash: 2ef8f322ff17eeb5d75d3cc8e4f8604f02d4ef0e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366535"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Vytvoření zabezpečeného clusteru Service Fabric Linux v Azure

Tento příkaz vytvoří certifikát podepsaný svým držitelem (self-signed certificate), přidá ho do trezoru klíčů a stáhne certifikát místně.  Nový certifikát se použije k zabezpečení clusteru při nasazování.  Nemusíte vytvářet nový certifikát, místo toho můžete použít certifikát stávající.  V obou případech musí název subjektu certifikátu odpovídat doméně, kterou používáte pro přístup ke clusteru Service Fabric. Tato shoda je vyžadována k poskytování TLS pro koncové body správy HTTPS clusteru a Průzkumník prostředků infrastruktury služeb. Certifikát TLS/SSL nelze získat od certifikační `.cloudapp.azure.com` autority pro doménu. Pro svůj cluster musíte získat název vlastní domény. Pokud požádáte o certifikát od certifikační autority, musí název subjektu certifikátu odpovídat názvu vlastní domény, který používáte pro svůj cluster.

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

## <a name="next-steps"></a>Další kroky

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
