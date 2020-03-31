---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175010"
---
V následující tabulce jsou uvedeny možné kombinace nahrávání a zachycení linuxových generalizovaných a specializovaných bitových kopií operačního systému. Kombinace, které se budou zpracovávat bez chyb, jsou označeny písmenem Y a ty, které budou vyvolávat chyby, jsou označeny písmenem N. Příčiny a řešení různých chyb, které narazíte, jsou uvedeny pod tabulkou.

| Operační systém | Nahrát spec. | Nahrát gen. | Zachyťte specifikaci. | Zachytit gen. |
| --- | --- | --- | --- | --- |
| Linux gen. |<sup>1.</sup> |Ano |Č.<sup>3</sup> |Ano |
| Linux spec. |Ano |<sup>2.</sup> |Ano |N<sup>4</sup> |

