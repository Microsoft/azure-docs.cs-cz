---
title: Seznam všech úloh Azure Import/Export | MicrosoftDocs
description: Zjistěte, jak zobrazit seznam všech úloh služby Azure Import/Export v rámci předplatného.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 017208c6fca7c4e55a45070f5aa21652e83e7e8d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462913"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Vytváření výčtu úloh ve službě Azure Import/Export
Chcete-li vytvořit výčet všech úloh v rámci předplatného, zavolejte [vypisovat úlohy](/rest/api/storageimportexport/jobs#Jobs_List) operace. `List Jobs` Vrátí seznam úloh, jakož i následující atributy:

-   Typ úlohy (Import nebo Export)

-   Aktuální stav úlohy

-   Úloha přidružený k tomuto účtu úložiště

## <a name="next-steps"></a>Další postup

* [Pomocí rozhraní REST API služby Import/Export](storage-import-export-using-the-rest-api.md)
