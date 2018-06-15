---
title: Seznam všech úloh Azure Import/Export | MicrosoftDocs
description: Naučte se seznam všech úloh služby Azure Import/Export v předplatném.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873699"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Vytváření výčtu úloh ve službě Azure Import/Export
Chcete-li vytvořit výčet všech úloh v předplatném, volejte [seznamu úloh](/rest/api/storageimportexport/jobs#Jobs_List) operaci. `List Jobs`Vrátí seznam úloh a také následující atributy:

-   Typ úlohy (Import nebo Export)

-   Aktuální stav úlohy

-   Úloha přidruženému účtu úložiště

## <a name="next-steps"></a>Další kroky

* [Pomocí REST API služby importu a exportu](storage-import-export-using-the-rest-api.md)
