---
title: Azure Data Factory mapování toku dat existuje transformace
description: Azure Data Factory mapování toku dat existuje transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734485"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory mapování toku dat existuje transformace

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace Exists je řádek transformace, která zastaví nebo povolí řádků ve vašich datech a procházet skrz filtrování. Existuje transformace je podobný ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS```. Po transformaci filtru, bude obsahovat výsledný řádky z datového proudu buď všechny řádky, pokud existují hodnoty sloupce ze zdroje 1 ve zdroji 2 nebo neexistují ve zdroji 2.

![Existuje nastavení](media/data-flow/exsits.png "existuje 1")

Zvolte druhý zdroj pro vaše Exists tak, aby tok dat můžete porovnat hodnoty z Stream 1 na Stream 2.

Vyberte sloupec, ze zdroje 1 a 2 zdroj jehož hodnoty, které chcete kontrolovat proti Exists nebo neexistuje.
