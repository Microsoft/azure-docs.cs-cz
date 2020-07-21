---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535744"
---
- K objektu přístupu k disku může být propojena pouze jedna virtuální síť.
- Vaše virtuální síť musí být ve stejném předplatném jako objekt pro přístup k disku, který je má propojit.
- Současně se stejným objektem pro přístup k disku můžete importovat nebo exportovat až 10 disků nebo snímků současně.
- Nemůžete požadovat ruční schválení k propojení virtuální sítě s objektem přístupu k disku.
- Rozdílová schopnost není podporována pro přírůstkové snímky, které jsou přidruženy k objektu přístupu k disku.