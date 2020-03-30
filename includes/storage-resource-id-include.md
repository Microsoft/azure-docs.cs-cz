---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68249921"
---
ID prostředku Azure AD označuje cílovou skupinu, pro kterou token, který je vydán slouží k poskytování přístupu k prostředku Azure. V případě Azure Storage id prostředku může být specifické pro jeden účet úložiště nebo se může vztahovat na libovolný účet úložiště. Následující tabulka popisuje hodnoty, které můžete zadat pro ID prostředku:

|ID prostředku  |Popis  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Koncový bod služby pro daný účet úložiště. Tato hodnota slouží k získání tokenu pro autorizaci požadavků na tento konkrétní účet azure storage a pouze služby. Nahraďte hodnotu v závorkách názvem účtu úložiště.      |
|`https://storage.azure.com/`     | Slouží k získání tokenu pro autorizaci požadavků na libovolný účet Azure Storage.        |
