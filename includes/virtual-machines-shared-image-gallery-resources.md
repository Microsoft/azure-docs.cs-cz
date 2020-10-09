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
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82788981"
---
| Prostředek | Popis|
|----------|------------|
| **Zdroj obrázku** | Toto je prostředek, který se dá použít k vytvoření **verze image** v galerii imagí. Zdrojem imagí může být existující virtuální počítač Azure, který je [zobecněný nebo specializovaný](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images), spravovaná bitová kopie, snímek nebo verze image v jiné galerii imagí. |
| **Galerie imagí** | Podobně jako u Azure Marketplace je **Galerie imagí** úložiště pro správu a sdílení imagí, ale Vy řídíte, kdo má přístup. |
| **Definice obrázku** | Definice imagí se vytvářejí v rámci Galerie a obsahují informace o imagi a požadavcích na jejich interní používání. To zahrnuje, zda se jedná o obrázek Windows nebo Linux, poznámky k verzi a minimální a maximální požadavky na paměť. Je definicí typu obrázku. |
| **Verze image** | **Verze image** je to, co použijete k vytvoření virtuálního počítače při použití galerie. V případě potřeby můžete mít v prostředí k dispozici více verzí bitové kopie. Podobně jako u spravované image při použití **verze image** k vytvoření virtuálního počítače se verze image používá k vytvoření nových disků pro virtuální počítač. Verze bitové kopie lze použít několikrát. |