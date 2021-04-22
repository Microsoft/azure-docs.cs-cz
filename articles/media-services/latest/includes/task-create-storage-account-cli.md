---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 44c349afe4bee4762b6dc2564c200f68f3296cc4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893194"
---
<!-- ### Create a storage account -->

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Další informace o tom, jak se ve službě Media Services používají účty úložiště, najdete v tématu [Účty Storage](../storage-account-concept.md).

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a můžete mít libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). Účty jen pro objekty blob nejsou povolené jako **primární**. Další informace o účtech úložiště najdete v článku [Možnosti účtů Azure Storage](../../../storage/common/storage-account-overview.md). 

V tomto příkladu vytvoříme účet Pro obecné účely v2 (Standard LRS). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS` . Když však vybíráte SKU pro produkci, měli byste zvážit, `--sku Standard_RAGRS` , což zajišťuje geografickou replikaci pro kontinuitu podnikových prostředí. Další informace najdete v tématu [účty úložiště](/cli/azure/storage/account).

Následující příkaz vytvoří účet Storage, který se přidruží k účtu Media Services. Ve skriptu níže nahraďte `storageaccountforams` vlastním názvem jedinečnou s délkou kratší než 24 znaků. `amsResourceGroup` musí odpovídat hodnotě, kterou jste zadali pro skupinu prostředků v předchozím kroku.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
