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
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171089"
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

Existující prostředek můžete také přesunout do skupiny umístění blízkosti. Při přesunu prostředku do skupiny umístění blízkosti byste měli nejprve zastavit (zrušit přidělení) Asset, protože se znovu nasadí do jiného datového centra v oblasti, aby splňovalo omezení umístění. 

V případě sad dostupnosti a sady škálování virtuálních počítačů byste měli nastavit skupinu umístění blízkosti na úrovni prostředků, nikoli na jednotlivých virtuálních počítačích. 

Skupina umístění blízkosti je omezení pro společné umístění, nikoli mechanizmus připnutí. Je připnutý ke konkrétnímu datovému centru s nasazením prvního prostředku, který ho bude používat. Jakmile se všechny prostředky, které používají skupinu umístění blízkosti, se zastavily (oddělují) nebo se odstraní, už se připnuté. Proto při použití skupiny umístění blízkosti s více řadami virtuálních počítačů je důležité určit všechny požadované typy předem v šabloně, pokud je to možné, nebo postupovat podle pořadí nasazení, které vám vylepšit úspěšnost nasazení. Pokud se nasazení nepovede, restartujte nasazení s velikostí virtuálního počítače, která se nezdařila, protože se nasadila první velikost.


## <a name="best-practices"></a>Osvědčené postupy 
- Pro nejnižší latenci používejte skupiny umístění pro Proximity společně s akcelerovanými síťovými službami. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými sítěmi](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)službami.
- Nasaďte všechny velikosti virtuálních počítačů v jediné šabloně. Abyste se vyhnuli vykládku na hardware, který nepodporuje všechny SKU virtuálních počítačů a velikosti, které požadujete, zahrňte všechny aplikační vrstvy do jediné šablony, aby se všechny nasadily ve stejnou dobu.
- Pokud provádíte skriptování nasazení pomocí PowerShellu, CLI nebo sady SDK, můžete získat `OverconstrainedAllocationRequest`chyby přidělení. V takovém případě byste měli zastavit nebo zrušit přidělení všech existujících virtuálních počítačů a změnit pořadí ve skriptu nasazení tak, aby začínalo s SKU nebo velikostmi virtuálního počítače, které selhaly. 
- Při použití existující skupiny umístění, ze které se virtuální počítače odstranily, počkejte, než se odstranění dokončí, než do něj přidáte virtuální počítače.
- Pokud je latence vaše první priorita, umístěte virtuální počítače do skupiny umístění blízkosti a do celého řešení v zóně dostupnosti. Pokud je odolnost vaše nejvyšší prioritou, rozšíří se vaše instance napříč několika zónami dostupnosti (jedna skupina umístění s jedním okolím nemůže zahrnovat zóny).