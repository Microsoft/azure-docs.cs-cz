---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608141"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Verze služby Azure Storage SDK ve funkcích 1.x

Ve funkcích 1.x, úložiště triggerů a vazeb použít 7.2.1 verzi sady SDK služby Azure Storage ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) balíček NuGet). Pokud budete odkazovat na jinou verzi sady SDK služby Storage a vytvoření vazby na typ sady SDK služby Storage ve vašich signatura funkce, modul runtime služby Functions zprávu, že nelze vytvořit vazbu k danému typu. Řešení se ujistěte se, že odkazy projektu [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
