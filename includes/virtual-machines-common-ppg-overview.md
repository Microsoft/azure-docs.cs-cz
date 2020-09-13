---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: c7e3c9292b53aeb073e11a5293459e39a22ca81d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89570222"
---
Umístění virtuálních počítačů v jedné oblasti omezuje fyzickou vzdálenost mezi instancemi. Jejich umístění v rámci jedné zóny dostupnosti se také fyzicky přiblíží dohromady. Pokud se ale nároky na Azure rozroste, může jedna zóna dostupnosti zahrnovat několik fyzických datových center, což může způsobit, že vaše aplikace bude mít vliv na latenci sítě. 

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci skupiny umístění blízkosti.

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


- Nízká latence mezi samostatnými virtuálními počítači.
- Nízká latence mezi virtuálními počítači v jedné skupině dostupnosti nebo sadou škálování virtuálního počítače. 
- Nenízká latence mezi samostatnými virtuálními počítači, virtuálními počítači v několika skupinách dostupnosti nebo několika sadami škálování. K spojování vícevrstvé aplikace můžete mít v jedné skupině umístění více výpočetních prostředků. 
- Nízká latence mezi několika aplikačními vrstvami, které používají různé typy hardwaru. Například spuštění back-endu pomocí řady M-Series ve skupině dostupnosti a front-Series instance řady D-Series v rámci skupiny škálování v jedné skupině umístění v blízkosti.


![Grafika pro skupiny umístění pro Proximity](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Používání skupin umístění blízkosti 

Skupina umístění blízkosti je nový typ prostředku v Azure. Musíte ho vytvořit ještě před tím, než ho použijete s jinými prostředky. Po vytvoření se dá použít s virtuálními počítači, skupinami dostupnosti nebo Virtual Machine Scale Sets. Skupina umístění blízkosti se určuje při vytváření výpočetních prostředků, které poskytují ID skupiny umístění blízkosti. 

Existující prostředek můžete také přesunout do skupiny umístění blízkosti. Při přesunu prostředku do skupiny umístění blízkosti byste měli nejprve zastavit (zrušit přidělení) Asset, protože se znovu nasadí do jiného datového centra v oblasti, aby bylo splněno omezení pro společné umístění. 

V případě sad dostupnosti a sady škálování virtuálních počítačů byste měli nastavit skupinu umístění blízkosti na úrovni prostředků, nikoli na jednotlivých virtuálních počítačích. 

Skupina umístění blízkosti je omezení pro společné umístění, nikoli mechanizmus připnutí. Je připnutý ke konkrétnímu datovému centru s nasazením prvního prostředku, který ho bude používat. Jakmile se všechny prostředky, které používají skupinu umístění blízkosti, se zastavily (oddělují) nebo se odstraní, už se připnuté. Proto při použití skupiny umístění blízkosti s více řadami virtuálních počítačů je důležité určit všechny požadované typy předem v šabloně, pokud je to možné, nebo postupovat podle pořadí nasazení, které vám vylepšit úspěšnost nasazení. Pokud se nasazení nepovede, restartujte nasazení s velikostí virtuálního počítače, která se nezdařila, protože se nasadila první velikost.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Co očekávat při používání skupin umístění blízkosti 
Skupiny umístění pro Proximity nabízí společné umístění ve stejném datovém centru. Protože však skupiny umístění blízkosti označují dodatečné omezení nasazení, může dojít k chybám přidělení. K dispozici je několik případů použití, kde se při použití skupin umístění blízkosti můžou zobrazit chyby přidělení:

- Když na prvním virtuálním počítači požádáte ve skupině umístění blízkosti, bude se automaticky vybrat datové centrum. V některých případech se může stát, že druhý požadavek na jinou SKLADOVOU položku virtuálního počítače selže, pokud v tomto datovém centru neexistuje. V tomto případě se vrátí **OverconstrainedAllocationRequest** chyba. Pokud se tomu chcete vyhnout, zkuste změnit pořadí, ve kterém nasazujete své SKU, nebo použijte oba prostředky nasazené s jednou šablonou ARM.
-   V případě elastických úloh, kde můžete přidat a odebrat instance virtuálních počítačů, které mají omezení skupiny umístění blízkosti, může dojít k selhání splnění žádosti s výsledkem **AllocationFailure** chyby. 
- Zastavení (zrušení přidělení) a spuštění virtuálních počítačů podle potřeby je další způsob, jak dosáhnout pružnosti. Vzhledem k tomu, že kapacita není po zastavení (přidělení) virtuálního počítače zachovaná, může dojít k chybě **AllocationFailure** .


## <a name="best-practices"></a>Osvědčené postupy 
- Pro nejnižší latenci používejte skupiny umístění pro Proximity společně s akcelerovanými síťovými službami. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými sítěmi](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)službami.
- Nasaďte všechny velikosti virtuálních počítačů v jediné šabloně. Abyste se vyhnuli vykládku na hardware, který nepodporuje všechny SKU virtuálních počítačů a velikosti, které požadujete, zahrňte všechny aplikační vrstvy do jediné šablony, aby se všechny nasadily ve stejnou dobu.
- Pokud provádíte skriptování nasazení pomocí PowerShellu, CLI nebo sady SDK, může se zobrazit chyba přidělení `OverconstrainedAllocationRequest` . V takovém případě byste měli zastavit nebo zrušit přidělení všech existujících virtuálních počítačů a změnit pořadí ve skriptu nasazení tak, aby začínalo s SKU nebo velikostmi virtuálního počítače, které selhaly. 
- Při použití existující skupiny umístění, ze které se virtuální počítače odstranily, počkejte, než se odstranění dokončí, než do něj přidáte virtuální počítače.
- Pokud je latence vaše první priorita, umístěte virtuální počítače do skupiny umístění blízkosti a do celého řešení v zóně dostupnosti. Pokud je odolnost vaše nejvyšší prioritou, rozšíří se vaše instance napříč několika zónami dostupnosti (jedna skupina umístění s jedním okolím nemůže zahrnovat zóny).
