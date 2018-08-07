---
title: Seznam všech úloh Azure Import/Export | MicrosoftDocs
description: Zjistěte, jak zobrazit seznam všech úloh služby Azure Import/Export v rámci předplatného.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 69daac71b69969a7ad9acfeb7095053f8138bf53
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520876"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Vytváření výčtu úloh ve službě Azure Import/Export
Chcete-li vytvořit výčet všech úloh v rámci předplatného, zavolejte [vypisovat úlohy](/rest/api/storageimportexport/jobs#Jobs_List) operace. `List Jobs` Vrátí seznam úloh, jakož i následující atributy:

-   Typ úlohy (Import nebo Export)

-   Aktuální stav úlohy

-   Úloha přidružený k tomuto účtu úložiště

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
