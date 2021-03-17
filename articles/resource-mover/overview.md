---
title: Co je to Azure Resource stěhovací?
description: Přečtěte si o službě Azure Resource stěhovací
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820159"
---
# <a name="what-is-azure-resource-mover"></a>Co je to Azure Resource stěhovací?

Tento článek poskytuje přehled služby Azure Resource stěhovací. Resource stěhovací pomůže přesunout prostředky Azure mezi oblastmi Azure.

Prostředky můžete přesunout do různých oblastí Azure:

- **Zarovnat ke konkrétnímu spuštění oblasti**: přesunout prostředky do nově zavedené oblasti Azure, která nebyla dříve dostupná.
- **Zarovnání pro služby/funkce**: přesunout prostředky, abyste mohli využívat služby nebo funkce, které jsou k dispozici v konkrétní oblasti.
- **Reakce na obchodní vývoj**: Přesunutí prostředků do oblasti v reakci na změny v podniku, jako je fúze nebo akvizice.
- **Zarovnání k blízkosti**: Přesunutí prostředků do místní oblasti do vaší firmy.
- **Splnění požadavků na data**: Přesunutí prostředků pro zajištění souladu s požadavky na data na místě nebo podle potřeby klasifikace dat.
- **Reakce na požadavky na nasazení**: přesunout prostředky, které byly nasazeny chybně, nebo je přesunout v reakci na kapacitu kapacity.
- **Reakce na vyřazení z provozu**: Přesunutí prostředků z důvodu vyřazení oblasti z provozu.


## <a name="why-use-resource-mover"></a>Proč používat Resource stěhovací?

Resource stěhovací poskytuje:

- Jediné centrum pro přesouvání prostředků napříč oblastmi.
- Zkrácená doba přesunutí a složitost. Všechno, co potřebujete, je na jednom místě.
- Jednoduché a konzistentní prostředí pro přesun různých typů prostředků Azure.
- Snadný způsob, jak identifikovat závislosti mezi prostředky, které chcete přesunout. To vám pomůže přesunout související prostředky dohromady, takže vše funguje podle očekávání v cílové oblasti, a to po přesunutí.
- Automatické vyčištění prostředků ve zdrojové oblasti, pokud je chcete po přesunutí odstranit.
- Testování. Můžete si vyzkoušet přesunutí a pak ho zahodit, pokud nechcete provést úplné přesunutí.

## <a name="move-across-regions"></a>Přesun mezi oblastmi

Chcete-li přesunout prostředky mezi oblastmi, vyberte prostředky, které chcete přesunout. Resource stěhovací tyto prostředky ověří a všechny závislosti, které mají na jiných prostředcích, se vyřeší. Pokud existují závislosti, máte několik možností:
- Přesuňte závislé prostředky do cílové oblasti.
- Nepřesouvat závislé prostředky, ale místo toho použijte ekvivalentní prostředky v cílové oblasti.

Po vyřešení všech závislostí vás dovedete pomocí jednoduchého procesu přesunutí.

1. Zahájíte prvotní přesun.
2. Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunout a dokončit přesunutí, nebo zrušit přesunutí.
3. Po dokončení přesunu se můžete rozhodnout, jestli chcete prostředky odstranit ze zdrojového umístění.

Prostředky můžete přesouvat mezi oblastmi v centru pro dodávání prostředků nebo v rámci skupiny prostředků. [Další informace](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Jaké prostředky je možné přesouvat mezi oblastmi?

Pomocí nástroje Resource Centre můžete aktuálně přesouvat následující prostředky v různých oblastech:

- Virtuální počítače Azure a přidružené disky
- Šifrované virtuální počítače Azure a přidružené disky. To zahrnuje virtuální počítače s povolenou službou Azure Disk Encryption a virtuální počítače Azure s použitím výchozího šifrování na straně serveru (s klíči spravovanými platformou a s klíči spravovanými zákazníky).
- Síťové karty
- Skupiny dostupnosti 
- Virtuální sítě Azure 
- Veřejné IP adresy
- Skupiny zabezpečení sítě (NSG)
- Interní a veřejné nástroje pro vyrovnávání zatížení 
- Databáze SQL Azure a elastické fondy


## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](about-move-process.md) o komponentách Resource stěhovací a procesu přesunutí.
