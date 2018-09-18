---
title: Účty úložiště ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek popisuje, jak služba Media Services využívá účty úložiště.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 883aecaa82f1a83af22bf345d364c2b43776e559
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732205"
---
# <a name="storage-accounts"></a>Účty úložiště

Při vytváření účtu Media Services je nutné zadat název prostředku účtu Azure Storage. Zadaný účet úložiště se připojí k vašemu účtu Media Services. 

Musí mít jeden **primární** účtu úložiště a můžete mít libovolný počet **sekundární** účty úložiště přidružené k účtu Media Services. Služba Media Services podporuje účty **General-purpose v2** (GPv2) nebo **General-purpose v1** (GPv1). 

>[!NOTE]
> Účty jen pro objekty blob nejsou povolené jako **primární**. 

Doporučujeme používat účty GPv2, takže můžete využít vybrat mezi horkou a studenou úrovní úložiště. Další informace o účtech úložiště najdete v tématu [přehled účtu Azure Storage](../../storage/common/storage-account-overview.md). 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V Media Services v3 rozhraní API úložiště slouží k nahrání souborů. Chcete-li zjistit, jak použít rozhraní API úložiště pomocí služby Media Services k nahrání vstupních souborů, přečtěte si [vytvořit vstup úlohy z místního souboru](job-input-from-local-file-how-to.md). 

> [!Note]
> By se neměly pokoušet ke změně obsahu kontejnery objektů blob, které byly generovány pomocí sady Media Services SDK bez použití rozhraní API služby Media Services.

## <a name="next-steps"></a>Další postup

Zjistěte, jak připojit účet úložiště k účtu Media Services, najdete v článku [vytvořit účet](create-account-cli-quickstart.md).
