---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bf01df2307796831dee602dad30455a4922ef90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98873178"
---
| Prostředek | Popis|
|----------|------------|
| **Zdroj obrázku** | Toto je prostředek, který se dá použít k vytvoření **verze image** v galerii imagí. Zdrojem imagí může být existující virtuální počítač Azure, který je [zobecněný nebo specializovaný](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images), spravovaná bitová kopie, snímek nebo verze image v jiné galerii imagí. |
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Definice imagí se vytvářejí v rámci Galerie a obsahují informace o imagi a požadavcích na jejich interní používání. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |