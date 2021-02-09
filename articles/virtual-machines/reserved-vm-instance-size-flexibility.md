---
title: Flexibilita velikosti virtuálního počítače – Azure Reserved VM Instances
description: Zjistěte, na jaké velikosti se sleva na rezervaci vztahuje, když jste rezervovali instanci virtuálního počítače.
author: yashesvi
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/02/2021
ms.author: yashar
ms.openlocfilehash: cf8f2f794be4340de4419457872ed5ff4bb4a686
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980435"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů

Když si koupíte rezervovanou instanci virtuálního počítače, můžete se rozhodnout optimalizovat pro flexibilitu velikosti instance nebo prioritu kapacity. Další informace o nastavení a změně nastavení optimalizace pro rezervované instance virtuálních počítačů najdete v tématu [Změna nastavení optimalizace pro rezervované instance virtuálních počítačů](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).

U rezervované instance virtuálního počítače, která je optimalizovaná pro flexibilitu velikosti instance, se rezervace, kterou koupíte, může vztahovat na velikosti virtuálních počítačů ve stejné skupině flexibility velikosti instance. Pokud třeba koupíte rezervaci pro velikost virtuálního počítače, která je uvedená v DSv2 řadě, například Standard_DS3_v2, sleva rezervace se může vztahovat na jiné velikosti, které jsou uvedené ve stejné skupině flexibility velikosti instance:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Tato sleva se ale nevztahuje na velikosti virtuálních počítačů, které jsou uvedené v různých velikostech, jako jsou SKU v DSv2 Series s vysokou pamětí: Standard_DS11_v2, Standard_DS12_v2 a tak dále.

V rámci skupiny flexibilita velikosti instance platí, že počet virtuálních počítačů, na které se sleva rezervace vztahuje, závisí na velikosti virtuálního počítače, kterou vyberete při nákupu rezervace. Závisí také na velikostech virtuálních počítačů, které používáte. Sloupec poměr porovnává relativní nároky na velikost každého virtuálního počítače v dané skupině flexibilita velikosti instancí. Pomocí hodnoty poměru můžete vypočítat, jak se sleva rezervace vztahuje na virtuální počítače, které používáte.

## <a name="examples"></a>Příklady

V následujících příkladech se používají velikosti a poměry v tabulce DSv2-Series.

Koupíte rezervovanou instanci virtuálního počítače s velikostí Standard_DS4_v2, kde poměr nebo relativní nároky v porovnání s ostatními velikostmi v této sérii jsou 8.

- Scénář 1: spuštění osmi 8 Standard_DS1_v2 virtuálních počítačů s poměrem 1. Vaše sleva na rezervaci se vztahuje na všechny 8 těchto virtuálních počítačů.
- Scénář 2: spuštění dvou virtuálních počítačů velikosti Standard_DS2_v2 s poměrem 2. Také spusťte virtuální počítač velikosti Standard_DS3_v2 s poměrem 4. Celkové nároky jsou 2 + 2 + 4 = 8. Proto se sleva vaší rezervace vztahuje na všechny tři tyto virtuální počítače.
- Scénář 3: spuštění jednoho Standard_DS5_v2 s poměrem 16. Vaše sleva za rezervaci se vztahuje na polovinu výpočetních nákladů tohoto virtuálního počítače.

V následujících částech se dozvíte, jaké velikosti jsou ve stejné skupině řad, když koupíte rezervovanou instanci virtuálního počítače optimalizovanou pro flexibilitu velikosti instance.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Poměr flexibility velikosti instancí pro virtuální počítače 

Sdílený svazek clusteru dál má flexibilní skupiny, ArmSkuName a poměry velikostí instancí.  

[Poměr flexibility velikosti instance](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Adresa URL souboru a schéma budeme uchovávat, aby bylo možné tento soubor zpracovat programově. Data budou také k dispozici prostřednictvím rozhraní API brzy.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [co je Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md).
