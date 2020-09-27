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
ms.openlocfilehash: ef573817927cf732da3426d802f8f26e2e9cd4ec
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398985"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Registrace připojení služby partnerského vztahu pomocí Azure CLI

Azure peering Service je síťová služba, která vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu. V tomto článku se dozvíte, jak zaregistrovat připojení služby partnerského vztahu pomocí Azure CLI.

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) hned teď.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Požadavky 

Musíte mít následující:

### <a name="azure-account"></a>Účet Azure

Musíte mít platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení připojení služby partnerského vztahu. Peering Service je prostředek v rámci předplatných Azure.

### <a name="connectivity-provider"></a>Poskytovatel připojení

Můžete pracovat s partnerem poskytovatele internetových služeb nebo s internetovým Exchangem a získat službu peering Service pro připojení vaší sítě k síti Microsoftu.

Ujistěte se, že jsou poskytovatelé připojení partneři s Microsoftem.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte své předplatné.

Pokud chcete zahájit konfiguraci, přihlaste se ke svému účtu Azure. Použijete-li možnost **vyzkoušet** Cloud Shell, jste přihlášeni automaticky. Použijte následující příklady, které vám pomůžou se připojit.

```azurecli-interactive
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

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
