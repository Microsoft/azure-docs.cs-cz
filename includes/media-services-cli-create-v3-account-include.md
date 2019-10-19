---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: da27f818bf368108568287f1ed1bbdae4c3902d4
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72560287"
---
## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Nejprve je nutné vytvořit účet Media Services. V této části se dozvíte, co potřebujete pro vytvoření účtu pomocí Azure CLI.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky, jako například účty Azure Media Services a přidružené účty Storage.

Můžete nahradit `amsResourceGroup` hodnotou.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Další informace o tom, jak se ve službě Media Services používají účty úložiště, najdete v tématu [Účty Storage](../articles/media-services/latest/storage-account-concept.md).

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../articles/storage/common/storage-account-options.md). 

V tomto příkladu vytvoříme účet Pro obecné účely v2 (Standard LRS). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS`. Při výběru skladové položky pro produkci byste ale měli zvážit, `--sku Standard_RAGRS`, což zajišťuje geografickou replikaci pro kontinuitu podnikových prostředí. Další informace najdete v tématu [účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services. V níže uvedeném skriptu můžete nahradit `storageaccountforams` vaší hodnotou. `amsResourceGroup` musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků v předchozím kroku. Název účtu úložiště musí mít délku menší než 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Následující příkaz Azure CLI vytvoří nový účet Media Services. Můžete nahradit následující hodnoty: `amsaccount` `storageaccountforams` (musí odpovídat hodnotě, kterou jste zadali pro účet úložiště) a `amsResourceGroup` (musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků).

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```
