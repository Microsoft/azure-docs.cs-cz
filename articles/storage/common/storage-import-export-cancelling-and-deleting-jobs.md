---
title: Zrušit a odstranit úlohu služby Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak rušit a odstraňovat úlohy pro službu Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e11cf974a5f33020c889844dd34f51612e13036e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521009"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>Rušení a odstraňování úloh Azure Import/Export

 Požádat o zrušit úlohu předtím, než se `Packaging` stavu, volání [aktualizovat vlastnosti úlohy](/rest/api/storageimportexport/jobs#Jobs_Update) operace a sady `CancelRequested` elementu `true`. Na základě best effort dojde ke zrušení úlohy. Pokud jsou jednotky probíhá přenos dat, data mohou nadále přenést i poté, co bylo vyžádáno zrušení.

 Zrušené úlohy se přesune do `Completed` stavu a uchovávají po dobu 90 dnů, kdy se odstraní.

 Pokud chcete odstranit úlohu, zavolejte [odstranit úlohu](/rest/api/storageimportexport/jobs#Jobs_Delete) operace předtím, než byla odeslaná úlohy (to znamená, když se úloha aktualizace zobrazí v `Creating` stavu). Můžete také odstranit úlohu, když je v `Completed` stavu. Po odstranění úlohy, její informace a stav již nejsou přístupná přes rozhraní REST API nebo na webu Azure portal.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
