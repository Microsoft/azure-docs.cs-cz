---
title: Účty úložiště ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek popisuje, jak služba Media Services využívá účty úložiště.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: f5fc1385bff92851db81cd4ed397d66cb8a832f2
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54350927"
---
# <a name="storage-accounts"></a>Účty úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. Účet Media Services a přidružený účet úložiště musí být součástí stejné datové centrum ve stejné skupině prostředků.

Ke svému účtu Media Services musíte mít přidružený jeden **primární** účet úložiště a libovolný počet **sekundárních** účtů úložiště. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). 

>[!NOTE]
> Účty jen pro objekty blob nejsou povolené jako **primární**. 

Doporučujeme používat účty GPv2, takže můžete využít vybrat mezi horkou a studenou úrovní úložiště. Další informace o účtech úložiště najdete v tématu [přehled účtu Azure Storage](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services v3 rozhraní API úložiště slouží k nahrání souborů. Chcete-li zjistit, jak použít rozhraní API úložiště pomocí služby Media Services k nahrání vstupních souborů, přečtěte si [vytvořit vstup úlohy z místního souboru](job-input-from-local-file-how-to.md). 

> [!Note]
> By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány pomocí sady Media Services SDK bez použití rozhraní API služby Media Services.

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit účet úložiště k účtu Media Services, najdete v článku [vytvořit účet](create-account-cli-quickstart.md).
