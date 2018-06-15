---
title: Účty úložiště ve službě Azure Media Services | Microsoft Docs
description: Tento článek popisuje, jak služba Media Services použije účty úložiště.
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
ms.openlocfilehash: 6d4c21867b0b46508f348300ae2b9553a75d23b2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788064"
---
# <a name="storage-accounts"></a>Účty úložiště

Při vytváření účtu Media Services, budete muset zadat název účtu prostředek služby Azure Storage. Zadaný účet úložiště je připojený ke svému účtu Media Services. 

Musí mít jeden **primární** účet úložiště a může mít libovolný počet **sekundární** účty úložiště přidružené k účtu Media Services. Služba Media Services podporuje **pro obecné účely v2** (GPv2) nebo **pro obecné účely v1** účty (GPv1). 

>[!NOTE]
> Pouze účty BLOB nejsou povoleny jako **primární**. 

Doporučujeme vám, že používáte GPv2, abyste mohli využívat výhod výběru mezi za a cool vrstev úložiště. Další informace o účtech úložiště najdete v tématu [možnosti účtu Azure Storage](../../storage/common/storage-account-options.md). 

## <a name="assets-in-a-storage-account"></a>Prostředky v účtu úložiště

V v3 Media Services používají rozhraní API úložiště k nahrání souborů. Chcete-li zjistit, jak použít rozhraní API úložiště pomocí služby Media Services k nahrání vaše vstupní soubory, podívejte se na [vytvořit úlohu vstupní z místního souboru](job-input-from-local-file-how-to.md). 

> [!Note]
> Chcete-li změnit obsah kontejnery objektů blob, které byly vygenerovány pomocí sady Media Services SDK bez použití rozhraní API služby média byste neměli.

## <a name="next-steps"></a>Další postup

Pokud chcete dozvědět, jak připojit k účtu úložiště k účtu Media Services, přečtěte si téma [vytvoření účtu](create-account-cli-quickstart.md).
