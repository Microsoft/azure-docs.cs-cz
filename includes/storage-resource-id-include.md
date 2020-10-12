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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249921"
---
ID prostředku Azure AD indikuje cílovou skupinu, pro kterou se dá vydaný token použít k poskytnutí přístupu k prostředku Azure. V případě Azure Storage může být ID prostředku specifické pro jeden účet úložiště nebo může platit pro libovolný účet úložiště. Následující tabulka popisuje hodnoty, které můžete zadat pro ID prostředku:

|ID prostředku  |Description  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Koncový bod služby pro daný účet úložiště. Tuto hodnotu použijte k získání tokenu pro autorizaci požadavků na konkrétní Azure Storage účet a službu. Hodnotu v závorkách nahraďte názvem svého účtu úložiště.      |
|`https://storage.azure.com/`     | Slouží k získání tokenu pro autorizaci požadavků na libovolný účet Azure Storage.        |
