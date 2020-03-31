---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471447"
---
Když si koupíte rezervovanou instanci virtuálního počítače, můžete optimalizovat pro flexibilitu velikosti instance nebo prioritu kapacity. Další informace o nastavení nebo změně nastavení optimalizace pro rezervované instance virtuálních počítačů najdete [v tématu Změna nastavení optimalizace pro rezervované instance virtuálních počítačů](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

S rezervovanou instancí virtuálního počítače, která je optimalizovaná pro flexibilitu velikosti instance, se zakoupená rezervace může vztahovat na velikosti virtuálních počítačů ve stejné skupině flexibility velikosti instance. Pokud si například koupíte rezervaci pro velikost virtuálního počítače, která je uvedená v řadě DSv2, jako je Standard_DS5_v2, může se sleva na rezervaci vztahovat na další čtyři velikosti, které jsou uvedeny ve stejné skupině flexibility velikosti instance:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Ale tato rezervace sleva se nevztahuje na velikosti virtuálních počítačů, které jsou uvedeny v různých skupinách flexibility velikosti instance, jako jsou skum v DSv2 Series High Memory: Standard_DS11_v2, Standard_DS12_v2 a tak dále.

V rámci skupiny flexibility velikosti instance závisí počet virtuálních počítačů, na které se vztahuje sleva na rezervaci, na velikost virtuálního počítače, kterou vyberete při nákupu rezervace. Závisí také na velikosti virtuálních počítače, které máte spuštěné. Sloupec poměru porovnává relativní nároky pro každou velikost virtuálního počítače v této skupině flexibility velikosti instance. Pomocí hodnoty poměru můžete vypočítat, jak se sleva rezervace vztahuje na spuštěné virtuální chody.

## <a name="examples"></a>Příklady

Následující příklady používají velikosti a poměry v tabulce řady DSv2.

Zakoupení majela instanci rezervovaného virtuálního počítače s velikostí Standard_DS4_v2 kde poměr nebo relativní nároky ve srovnání s ostatními velikostmi v této řadě je 8.

- Scénář 1: Spusťte osm virtuálních počítače o velikosti Standard_DS1_v2 s poměrem 1. Vaše rezervace sleva se vztahuje na všech osm těchto virtuálních počítačích.
- Scénář 2: Spusťte dva virtuální počítače o velikosti Standard_DS2_v2 s poměrem 2. Také spustit Standard_DS3_v2 velikosti virtuálního provozu s poměrem 4. Celková půdorys je 2+2+4=8. Takže vaše rezervace sleva se vztahuje na všechny tři z těchto virtuálních počítačů.
- Scénář 3: Spusťte jednu Standard_DS5_v2 s poměrem 16. Vaše sleva na rezervaci se vztahuje na polovinu výpočetních nákladů virtuálního počítače.

Následující části ukazují, jaké velikosti jsou ve stejné skupině řad velikostí při nákupu vyhrazené instance virtuálního počítače optimalizované pro flexibilitu velikosti instance.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Poměr flexibility velikosti instance pro virtuální počítače 

CSV níže má skupiny flexibility velikosti instance, ArmSkuName a poměry.  

[Poměry flexibility velikosti instance](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Budeme mít url souboru a schéma pevné, takže můžete konzumovat tento soubor programově. Data budou také brzy k dispozici prostřednictvím rozhraní API.
