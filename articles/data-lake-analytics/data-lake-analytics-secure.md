---
title: Pro více uživatelů zabezpečení služby Azure Data Lake Analytics
description: Zjistěte, jak nakonfigurovat více uživatelů ke spouštění úloh v Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: c6b86e25602f36896855d2593952609904396879
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051580"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurace přístupu uživatelů k informace o úlohách na informace o úlohách v Azure Data Lake Analytics 

Ve službě Azure Data Lake Analytics můžete použít několik účtů nebo instanční objekty ke spouštění úloh. 

Aby tito uživatelé zobrazíte informace o úloze podrobné uživatelé muset být schopni číst obsah složky projektu. Úlohy složky jsou umístěny v `/system/` adresáře. 

Pokud nejsou nakonfigurovaná potřebná oprávnění, může se zobrazit chyba uživatele: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurace přístupu uživatelů k informacím o úloze

Můžete použít **Průvodce přidáním uživatele** nakonfigurovat seznamy ACL na složky. Další informace najdete v tématu [přidat nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Pokud potřebujete více detailní kontrolu nebo nutnosti skriptovat oprávnění, pak zabezpečení složky následujícím způsobem:

1. Udělení **provést** oprávnění (prostřednictvím přístupového seznamu ACL) pro kořenovou složku:
   - /
   
2. Udělení **provést** a **čtení** oprávnění (prostřednictvím přístupového seznamu ACL a výchozího seznamu ACL) pro složky, které obsahují složky projektu. Například pro konkrétní úlohu, který spustil 25. května 2018, těchto složek je potřeba přistupovat k:
   - / System
   - / systém/jobservice
   - /System/jobservice/Jobs
   - /System/jobservice/Jobs/Usql
   - /System/jobservice/Jobs/Usql/2018
   - /System/jobservice/Jobs/Usql/2018/05
   - /System/jobservice/Jobs/Usql/2018/05/25
   - /System/jobservice/Jobs/Usql/2018/05/25/11
   - /System/jobservice/Jobs/Usql/2018/05/25/11/01
   - systém/jobservice/úlohy/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Další postup
[Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user)
