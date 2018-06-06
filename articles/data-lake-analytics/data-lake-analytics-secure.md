---
title: Pro více uživatelů zabezpečení služby Azure Data Lake Analytics
description: Zjistěte, jak nakonfigurovat více uživatelů ke spuštění úloh v Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1d92e6d0e619584dedcbc9a66450c25dd1ac8b75
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701384"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurace přístupu uživatelů k informace o úlohách na informace o úlohách v Azure Data Lake Analytics 

V Azure Data Lake Analytics můžete použít více uživatelských účtů nebo objekty služby ke spuštění úloh. 

V pořadí pro tyto stejné uživatelům zobrazit informace o podrobné úlohy uživatelé musí být možné číst obsah složek úlohy. Úlohy složky jsou umístěny ve `/system/` adresáře. 

Pokud nejsou nakonfigurována potřebná oprávnění, může uživatel zobrazit chybu: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurace přístupu uživatelů k informace o úlohách

Můžete použít **Průvodce přidáním uživatele** nakonfigurovat seznamy ACL na složky. Další informace najdete v tématu [přidat nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Pokud potřebujete více přesná kontrola nebo potřeba skript oprávnění, pak zabezpečit složky následujícím způsobem:

1. Udělení **provést** oprávnění (prostřednictvím aplikace access seznamu ACL) v kořenové složce:
   - /
   
2. Udělení **provést** a **číst** oprávnění (prostřednictvím přístupu seznamu řízení přístupu a výchozí seznamu ACL) u složky, které obsahují složky úlohy. Například pro konkrétní úlohu, která byla spuštěna na 25 může 2018 tyto složky třeba získat přístup:
   - / System
   - / systém/jobservice
   - /System/jobservice/Jobs
   - /System/jobservice/Jobs/Usql
   - /System/jobservice/Jobs/Usql/2018
   - /System/jobservice/Jobs/Usql/2018/05
   - /System/jobservice/Jobs/Usql/2018/05/25
   - /System/jobservice/Jobs/Usql/2018/05/25/11
   - /System/jobservice/Jobs/Usql/2018/05/25/11/01
   - systému nebo jobservice/úlohy/Usql nebo 2018/05/25/11 nebo 01 nebo b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Další postup
[Přidání nového uživatele](data-lake-analytics-manage-use-portal.md#add-a-new-user)
