---
title: Nasazení aplikace Service Fabric sítě, která používá Azure Files svazek | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci, která používá Azure Files svazku se Service Fabric pomocí Azure CLI.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: fb9740efaa9a1033d6602180f5750f6fb550c048
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075977"
---
# <a name="deploy-a-service-fabric-mesh-application-that-uses-the-azure-files-volume"></a>Nasazení aplikace Service Fabric sítě, která používá Azure Files svazku
Tento příklad ukazuje použití metody svazky v kontejneru spuštěná v Azure Service Fabric mřížky. Jako součást této ukázce:

- Vytvoření sdílené složky s [soubory Azure](/azure/storage/files/storage-files-introduction) 
- Odkazovat na tuto sdílenou složku jako svazek pro instanci kontejneru, který nasadíme
  - Při spuštění kontejneru připojí tuto sdílenou složku jako na konkrétní místo v kontejneru
- Kód běžící uvnitř kontejneru do ní zapisuje do textového souboru
- Ověřte, zda že je soubor ve sdílené složce, která zálohuje svazku napsáno správně

## <a name="example-json-templates"></a>Příklad šablony JSON

Linux: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json)

Windows: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json)

## <a name="create-the-azure-files-file-share"></a>Vytvoření sdílené složky Azure Files

Postupujte podle pokynů [dokumentace ke službě Azure Files](/azure/storage/files/storage-how-to-create-file-share) k vytvoření sdílené složky pro aplikace pro použití.

## <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku mřížky na Service Fabric
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

K dokončení těchto kroků můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte nainstalovat Azure CLI verze 2.0.35 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pro instalaci nebo upgrade na nejnovější verzi rozhraní příkazového řádku, naleznete v tématu [instalace Azure CLI 2.0] [azure-cli-install].

Instalace modulu Azure Service Fabric mřížky CLI rozšíření. Ve verzi Preview je zapsán Azure Service Fabric mřížky CLI jako rozšíření rozhraní příkazového řádku Azure.

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="log-in-to-azure"></a>Přihlášení k Azure
Připojte se k Azure a nastavení předplatného.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků (RG) k nasazení v tomto příkladu nebo můžete použít existující skupinu prostředků a tento krok přeskočit. Verzi preview je dostupná jenom v `eastus` umístění.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus
```

## <a name="deploy-the-template"></a>Nasazení šablony
Vytvoření aplikace a související prostředky pomocí jedné z následujících příkazů.

Pro Linux:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json
```

Ve Windows:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json
```

Postupujte podle zobrazených výzev a zadejte název sdílené složky, název účtu a klíč účtu pro sdílenou složku Azure, která poskytuje svazku. V minutě vypršet, by měla vrátit příkazu s `"provisioningState": "Succeeded"`.

Parametr hesla v šabloně je `string` typ pro snadné použití. Zobrazí se na obrazovce, ve formátu prostého textu a stav nasazení.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Ověřte, že aplikace je možné použít svazku
Aplikace vytvoří soubor s názvem _data.txt_ ve sdílené složce (pokud ho ještě neexistuje). Obsah tohoto souboru je číslo, které aplikace se zvýší každých 30 sekund. Chcete-li ověřit, že tento příklad funguje správně, otevřete _data.txt_ pravidelně souboru a ověřte, že číslo se aktualizuje.

Stáhnout soubor pomocí libovolného nástroje, který umožňuje procházení sdílenou složku Azure Files. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) je příkladem tento nástroj.

## <a name="next-steps"></a>Další postup

- Zobrazit soubory Azure svazku ukázkovou aplikaci na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/azurefiles-volume).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).
