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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420277"
---
- K objektu přístupu k disku může být propojena pouze jedna virtuální síť.
- Vaše virtuální síť musí být ve stejném předplatném jako objekt pro přístup k disku, který je má propojit.
- Současně se stejným objektem pro přístup k disku můžete importovat nebo exportovat až 10 disků nebo snímků současně.
- Nemůžete požadovat ruční schválení k propojení virtuální sítě s objektem přístupu k disku.
- Rozdílová schopnost není podporována pro přírůstkové snímky, které jsou přidruženy k objektu přístupu k disku.
- AzCopy se nedá použít ke stažení virtuálního pevného disku nebo snímku, který je zabezpečený prostřednictvím privátních odkazů na účet úložiště. Pomocí AzCopy ale můžete stáhnout virtuální pevný disk do vašich virtuálních počítačů.
