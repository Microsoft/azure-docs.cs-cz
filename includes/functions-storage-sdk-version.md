---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675271"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Verze sady SDK úložiště Azure ve funkcích 1.x

Ve funkcích 1.x, úložiště triggerů a vazeb použijte 7.2.1 verzi sady SDK úložiště Azure ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) balíček NuGet). Pokud odkazujete na jiné verzi sady SDK úložiště a vytvoření vazby na typ sada SDK úložiště ve vaší podpis funkce, Functions runtime hlásit, že nelze vytvořit vazbu na daný typ. Řešení se přesvědčte, že vaše odkazy na projekt [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
