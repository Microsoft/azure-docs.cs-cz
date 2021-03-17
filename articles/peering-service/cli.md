---
title: Registrace připojení služby peering ve verzi Preview pomocí Azure CLI
description: Naučte se registrovat připojení služby partnerského vztahu pomocí Azure CLI.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540582"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrace připojení služby partnerského vztahu pomocí Azure CLI

Azure peering Service je síťová služba, která vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu. V tomto článku se dozvíte, jak zaregistrovat připojení služby partnerského vztahu pomocí Azure CLI.

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Spuštěním příkazu [az version](/cli/azure/reference-index#az_version) zjistěte verzi a závislé knihovny, které jsou nainstalované. Pokud chcete upgradovat na nejnovější verzi, spusťte [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="prerequisites"></a>Požadavky 

Musíte mít následující:

### <a name="azure-account"></a>Účet Azure

Musíte mít platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení připojení služby partnerského vztahu. Peering Service je prostředek v rámci předplatných Azure.

### <a name="connectivity-provider"></a>Poskytovatel připojení

Můžete pracovat s partnerem poskytovatele internetových služeb nebo s internetovým Exchangem a získat službu peering Service pro připojení vaší sítě k síti Microsoftu.

Ujistěte se, že jsou poskytovatelé připojení partneři s Microsoftem.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

### <a name="1-select-your-subscription"></a>1. Vyberte své předplatné.

Vyberte předplatné, pro které chcete zaregistrovat připojení ke službě peering Service.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Pokud ještě nemáte skupinu prostředků, musíte ji vytvořit předtím, než zaregistrujete připojení ke službě peering Service. Skupinu prostředků můžete vytvořit spuštěním následujícího příkazu:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Zaregistrujte své předplatné pomocí příznaku funkce a poskytovatele prostředků.

Než budete pokračovat postupem registrace připojení ke službě peering Service pomocí rozhraní příkazového řádku Azure, zaregistrujte své předplatné s použitím poskytovatele prostředků a příznaku funkce pomocí Azure CLI. Tady jsou uvedené příkazy rozhraní příkazového řádku Azure CLI:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. Zaregistrujte připojení ke službě peering Service.

Zaregistrujte připojení služby partnerského vztahu pomocí následující sady příkazů prostřednictvím Azure CLI. Tento příklad zaregistruje připojení partnerské služby s názvem myPeeringService.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. Zaregistrujte předponu.

Pomocí následujících příkazů pomocí Azure CLI Zaregistrujte předponu, kterou poskytuje poskytovatel připojení. Tento příklad registruje předponu s názvem myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
- Měření telemetrie najdete v tématu [měření telemetrie připojení](measure-connection-telemetry.md).
- Pokud chcete připojení zaregistrovat pomocí Azure PowerShell, přečtěte si téma [registrace připojení služby peering Service-Azure PowerShell](powershell.md).
- Pokud chcete připojení zaregistrovat pomocí Azure Portal, přečtěte si téma [registrace připojení služby peering Service-Azure Portal](azure-portal.md).
