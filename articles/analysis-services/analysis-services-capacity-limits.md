---
title: Limity prostředků a objektů služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje omezení prostředků a objektů pro server Služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f309c9863eb2f3065251537380a2977839f990d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573199"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limity prostředků a objektů služby Analysis Services

Tento článek popisuje omezení objektů prostředků a modelu.

## <a name="tier-limits"></a>Limity úrovně

Limity QPU a paměti pro vývojářské, základní a standardní úrovně najdete na [stránce s cenami služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limity objektů

Tyto limity jsou teoretické. Výkon se sníží při nižších číslech.

|Objekt|Maximální počet velikostí/čísel|  
|------------|----------------------------|  
|Databáze v instanci|16 000|  
|Kombinovaný počet tabulek a sloupců v databázi|16 000|  
|Řádky v tabulce|Unlimited<br /><br /> **Upozornění:** S omezením, že žádný sloupec v tabulce může mít více než 1 999 999 997 odlišné hodnoty.|  
|Hierarchie v tabulce|15,999|  
|Úrovně v hierarchii|15,999|  
|Relace|8 000|  
|Klíčové sloupce ve všech tabulkách|15,999|  
|Míry v tabulkách|2^31-1 = 2 147 483 647|  
|Buňky vrácené dotazem|2^31-1 = 2 147 483 647|  
|Zaznamenat velikost zdrojového dotazu|64 K|  
|Délka názvů objektů|512 znaků|  


