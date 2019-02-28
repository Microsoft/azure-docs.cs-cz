---
title: Nahrávání a úložiště pomocí Azure Media Services v cloudu | Dokumentace Microsoftu
description: Tento článek cloudu nahrávání a koncepty úložiště.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: cda029dd11e8cb4cb07e9fce7eef95d6d4d78d7e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960207"
---
# <a name="cloud-upload-and-storage"></a>Nahrávání na cloud a úložiště

Pokud chcete začít, správa, šifrování, kódování, analýza a streamování médií obsahu v Azure, budete muset vytvořit účet Media Services. Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Účet Media Services a všechny přidružené úložiště účty musí být ve stejném předplatném Azure. Doporučujeme, aby používaly účty úložiště ve stejném umístění jako účet Media Services, aby se zabránilo další latenci a data náklady na odchozí přenosy

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). 

>[!NOTE]
> Účty jen pro objekty blob nejsou povolené jako **primární**. 

Doporučujeme používat účty GPv2, takže můžete využít vybrat mezi horkou a studenou úrovní úložiště. Další informace o účtech úložiště najdete v tématu [přehled účtu Azure Storage](../../storage/common/storage-account-overview.md). 

Existují různé skladové položky, které můžete použít pro váš účet úložiště. Další informace najdete v tématu [účty úložiště](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Pokud chcete experimentovat s účty úložiště, použijte `--sku Standard_LRS`. Ale při výběru SKU pro produkční prostředí byste měli zvážit, `--sku Standard_RAGRS`, která poskytuje geografické replikace zajišťuje nepřetržitý chod podniků. 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services v3 rozhraní API úložiště slouží k nahrání souborů do prostředků. Další informace najdete v tématu [prostředky koncept](assets-concept.md).

> [!Note]
> By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány pomocí sady Media Services SDK bez použití rozhraní API služby Media Services.
 
## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit účet úložiště k účtu Media Services, najdete v článku [vytvořit účet](create-account-cli-quickstart.md).
