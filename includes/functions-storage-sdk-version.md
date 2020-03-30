---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198410"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Verze sady Azure Storage SDK ve funkcích 1.x

Ve funkcích 1.x se aktivační události a vazby úložiště používají verze 7.2.1 sady Azure Storage SDK (balíček[WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet). Pokud odkazujete na jinou verzi sady Storage SDK a vážete se na typ sady Storage SDK v podpisu funkce, může runtime Funkce hlásit, že se nemůže svázat s tímto typem. Řešením je ujistěte se, že váš projekt odkazuje na [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
