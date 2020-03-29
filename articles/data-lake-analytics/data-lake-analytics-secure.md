---
title: Zabezpečení Azure Data Lake Analytics pro více uživatelů
description: Zjistěte, jak nakonfigurovat více uživatelů pro spouštění úloh v Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813368"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurace přístupu uživatelů k informacím o úlohách k informacím o úlohách v Azure Data Lake Analytics 

V Azure Data Lake Analytics můžete ke spouštění úloh použít více uživatelských účtů nebo instancí. 

Aby titíž uživatelé mohli zobrazit podrobné informace o úloze, musí být schopni číst obsah složek úloh. Složky úloh jsou `/system/` umístěny v adresáři. 

Pokud nejsou nakonfigurována potřebná oprávnění, může se uživateli zobrazit chyba:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurace přístupu uživatelů k informacím o úloze

Pomocí Průvodce **přidáním uživatele** můžete nakonfigurovat akly ve složkách. Další informace naleznete [v tématu Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Pokud potřebujete podrobnější ovládací prvek nebo potřebujete skriptovat oprávnění, zabezpečte složky následujícím způsobem:

1. Udělit oprávnění **ke spuštění** (prostřednictvím přístupového seznamu ACL) v kořenové složce:
   - /
   
2. Udělit **oprávnění ke spuštění** a **čtení** (prostřednictvím přístupového seznamu ACL a výchozího seznamu ACL) ve složkách, které obsahují složky úloh. Například pro konkrétní úlohu, která běžela 25.
   - /systém
   - /system/jobservice
   - /system/jobservice/úlohy
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101b3d

## <a name="next-steps"></a>Další kroky
[Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user)
