---
title: Azure Analysis Services omezení prostředků a objektů | Microsoft Docs
description: Popisuje Azure Analysis Services omezení prostředků a objektů.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fe43602c66af72357e16822ee9d4b5a741d3f86
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298700"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services omezení prostředků a objektů

Tento článek popisuje omezení pro prostředky a objekty modelu.

## <a name="tier-limits"></a>Omezení vrstvy

Omezení QPU a paměti pro úrovně Developer, Basic a Standard najdete na [stránce s cenami Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Omezení objektu

Tato omezení jsou teoretická. Výkon se sníží na nižších číslech.

|Objekt|Maximální velikost/čísla|  
|------------|----------------------------|  
|Databáze v instanci|16 000|  
|Kombinovaný počet tabulek a sloupců v databázi|16 000|  
|Řádky v tabulce|Bez omezení<br /><br /> **Upozornění:** Omezením, že žádný jeden sloupec v tabulce nemůže mít více než 1 999 999 997 jedinečných hodnot.|  
|Hierarchie v tabulce|15 999|  
|Úrovně v hierarchii|15 999|  
|Relace|8 000|  
|Klíčové sloupce ve všech tabulkách|15 999|  
|Míry v tabulkách|2 ^ 31-1 = 2 147 483 647|  
|Buňky vrácené dotazem|2 ^ 31-1 = 2 147 483 647|  
|Velikost záznamu pro zdrojový dotaz|64 K|  
|Délka názvů objektů|512 znaků|  


