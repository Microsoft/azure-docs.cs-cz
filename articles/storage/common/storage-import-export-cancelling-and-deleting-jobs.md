---
title: Zrušit a odstranit úlohu Azure Import/Export | Microsoft Docs
description: Zjistěte, jak zrušit a odstraňovat úlohy pro službu Microsoft Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3524f1677baaa218b009b8498b851390c7b9da9a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Zrušení a odstranění úlohy Azure Import/Export

 Požádat o zrušit úlohu předtím, než je v `Packaging` stavu, volejte [vlastnosti úlohy aktualizace](/rest/api/storageimportexport/jobs#Jobs_Update) operace a sadu `CancelRequested` element `true`. Úloha je zrušena na základě typu best effort. Pokud jednotky probíhá přenos dat, dat pokračovat v přesunu, i když bylo vyžádáno zrušení.

 Zrušené úlohy je přesunuta do `Completed` stavu a je uložen za 90 dnů od této chvíle se odstraní.

 Chcete-li odstranit úlohu, volejte [odstranit úlohu](/rest/api/storageimportexport/jobs#Jobs_Delete) operace předtím, než má dodaný úlohy (to znamená, když úloha je v `Creating` stav). Můžete také odstranit úlohu, když je `Completed` stavu. Po odstranění úlohy její informace a stav již nejsou přístupné přes rozhraní REST API nebo portálu Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
