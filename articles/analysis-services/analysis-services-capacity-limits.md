---
title: Azure Analysis Services omezení prostředků a objektů | Microsoft Docs
description: Tento článek popisuje omezení prostředků a objektů pro server Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: beb4ca31b65d5de0b81030cdf3222418cb968060
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732002"
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
|Řádky v tabulce|Unlimited<br /><br /> **Upozornění:** Omezením, že žádný jeden sloupec v tabulce nemůže mít více než 1 999 999 997 jedinečných hodnot.|  
|Hierarchie v tabulce|15 999|  
|Úrovně v hierarchii|15 999|  
|Relace|8 000|  
|Klíčové sloupce ve všech tabulkách|15 999|  
|Míry v tabulkách|2 ^ 31-1 = 2 147 483 647|  
|Buňky vrácené dotazem|2 ^ 31-1 = 2 147 483 647|  
|Velikost záznamu pro zdrojový dotaz|64 K|  
|Délka názvů objektů|512 znaků|  


