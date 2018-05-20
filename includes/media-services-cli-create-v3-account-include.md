---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 3157f5db58be5735fa7b194393b0ab6d0e143a91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Nejprve je nutné vytvořit účet Media Services. Tato část uvádí, co potřebujete k vytvoření účtu pomocí CLI 2.0.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky, jako například účty Azure Media Services a přidružené účty Storage.

```azurecli-interactive
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../articles/storage/common/storage-account-options.md). 

Další informace o tom, jak se ve službě Media Services používají účty úložiště, najdete v tématu [Účty Storage](../articles/media-services/latest/storage-account-concept.md).

Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services. V níže uvedeném skriptu můžete nahradit `storageaccountforams` vaší hodnotou. Název účtu musí mít méně než 24 znaků.

```azurecli-interactive
az storage account create --name storageaccountforams \  
--kind StorageV2 \
--sku Standard_RAGRS \
--resource-group amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

Následující příkaz Azure CLI vytvoří nový účet Media Services. Můžete nahradit následující hodnoty: `amsaccount` `storageaccountforams` (musí odpovídat hodnotě, kterou jste zadali pro účet úložiště) a `amsResourceGroup` (musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků).

```azurecli-interactive
az ams account create --name amsaccount --resource-group amsResourceGroup --storage-account storageaccountforams
```
