---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 9a7de2b41c8016bdb7849cdef428d6f54a8ccf64
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005461"
---
U rezervované instance virtuálního počítače, která je optimalizovaná pro flexibilitu velikosti instance, se rezervace, kterou koupíte, může vztahovat na velikosti virtuálních počítačů ve stejné skupině flexibility velikosti instance. Pokud třeba koupíte rezervaci pro velikost virtuálního počítače, která je uvedená v DSv2 řadě, například Standard_DS5_v2, sleva rezervace se může vztahovat na ostatní čtyři velikosti, které jsou uvedené ve stejné skupině flexibility velikosti instance:

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
