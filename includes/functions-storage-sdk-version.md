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
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132384"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Verze služby Azure Storage SDK ve funkcích 1.x

Ve funkcích 1.x, úložiště triggerů a vazeb použít 7.2.1 verzi sady SDK služby Azure Storage ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) balíček NuGet). Pokud budete odkazovat na jinou verzi sady SDK služby Storage a vytvoření vazby na typ sady SDK služby Storage ve vašich signatura funkce, modul runtime služby Functions zprávu, že nelze vytvořit vazbu k danému typu. Řešení se ujistěte se, že odkazy projektu [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
