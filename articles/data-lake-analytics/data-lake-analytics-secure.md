---
title: Zabezpečení Azure Data Lake Analytics pro více uživatelů
description: Naučte se konfigurovat více uživatelů ke spouštění úloh v Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 52bdd80dba1d5de150f403199fbd5c14d188a410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220138"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurace přístupu uživatelů k informacím o úloze v Azure Data Lake Analytics 

V Azure Data Lake Analytics můžete ke spouštění úloh použít více uživatelských účtů nebo instančních objektů. 

Aby mohli uživatelé zobrazit podrobné informace o úlohách, musí být schopni číst obsah složek úloh. Složky úloh se nacházejí v `/system/` adresáři. 

Pokud nejsou potřebná oprávnění nakonfigurovaná, může se uživateli zobrazit chyba: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurace přístupu uživatelů k informacím o úloze

Můžete použít **Průvodce přidáním uživatele** ke konfiguraci seznamů řízení přístupu (ACL) ve složkách. Další informace najdete v tématu [Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Pokud potřebujete podrobnější kontrolu nebo potřebujete skriptovat oprávnění, zabezpečte tyto složky následujícím způsobem:

1. Udělte oprávnění k **provedení** (prostřednictvím seznamu ACL přístupu) v kořenové složce:
   - /
   
2. Udělte oprávnění ke **spouštění** a **čtení** (prostřednictvím seznamu ACL přístupu a výchozího seznamu ACL) ve složkách, které obsahují složky úloh. Například pro konkrétní úlohu, která byla spuštěna dne 25. května 2018, je nutné mít k dispozici tyto složky:
   - /
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Další kroky
[Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user)
