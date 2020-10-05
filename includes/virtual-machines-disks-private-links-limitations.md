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
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377389"
---
- K objektu přístupu k disku může být propojena pouze jedna virtuální síť.
- Vaše virtuální síť musí být ve stejném předplatném jako objekt pro přístup k disku, který je má propojit.
- Současně se stejným objektem pro přístup k disku můžete importovat nebo exportovat až 10 disků nebo snímků současně.
- Nemůžete požadovat ruční schválení k propojení virtuální sítě s objektem přístupu k disku.
- Přírůstkové snímky nelze exportovat, pokud jsou přidruženy k objektu přístupu k disku.
- AzCopy se nedá použít ke stažení virtuálního pevného disku nebo snímku, který je zabezpečený prostřednictvím privátních odkazů na účet úložiště. Pomocí AzCopy ale můžete stáhnout virtuální pevný disk do vašich virtuálních počítačů.
