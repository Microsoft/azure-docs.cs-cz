---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850324"
---
Umístění virtuálních počítačů v jedné oblasti omezuje fyzickou vzdálenost mezi instancemi. Jejich umístění v rámci jedné zóny dostupnosti se také fyzicky přiblíží dohromady. Pokud se ale nároky na Azure rozroste, může jedna zóna dostupnosti zahrnovat několik fyzických datových center, což může způsobit, že vaše aplikace bude mít vliv na latenci sítě. 

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci skupiny umístění blízkosti.

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


- Nízká latence mezi samostatnými virtuálními počítači.
- Nízká latence mezi virtuálními počítači v jedné skupině dostupnosti nebo sadou škálování virtuálního počítače. 
- Nenízká latence mezi samostatnými virtuálními počítači, virtuálními počítači v několika skupinách dostupnosti nebo několika sadami škálování. K spojování vícevrstvé aplikace můžete mít v jedné skupině umístění více výpočetních prostředků. 
- Nízká latence mezi několika aplikačními vrstvami, které používají různé typy hardwaru. Například spuštění back-endu pomocí řady M-Series ve skupině dostupnosti a front-Series instance řady D-Series v rámci skupiny škálování v jedné skupině umístění v blízkosti.

> [!IMPORTANT]
> Skupiny umístění blízkosti jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Skupiny umístění blízkosti nejsou v těchto oblastech k dispozici v průběhu verze Preview: **Japonsko – východ**, **Austrálie – východ** a **Indie – střed**


## <a name="best-practices"></a>Osvědčené postupy 
- Pro nejnižší latenci používejte skupiny umístění pro Proximity společně s akcelerovanými síťovými službami. Další informace najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými sítěmi](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)službami.
- Všechny velikosti virtuálních počítačů nasaďte v jedné šabloně Resource Manageru. Abyste se vyhnuli vykládku na hardware, který nepodporuje všechny SKU virtuálních počítačů a velikosti, které požadujete, zahrňte všechny aplikační vrstvy do jediné šablony, aby se všechny nasadily ve stejnou dobu.
- Pokud provádíte skriptování nasazení pomocí PowerShellu, CLI nebo sady SDK, může se zobrazit chyba `OverconstrainedAllocationRequest`přidělení. V takovém případě byste měli zastavit nebo zrušit přidělení všech existujících virtuálních počítačů a změnit pořadí ve skriptu nasazení tak, aby začínalo s SKU nebo velikostmi virtuálního počítače, které selhaly. 
- Při použití existující skupiny umístění, ze které se virtuální počítače odstranily, počkejte, než se odstranění dokončí, než do něj přidáte virtuální počítače.
- Pokud je latence vaše první priorita, umístěte virtuální počítače do skupiny umístění blízkosti a do celého řešení v zóně dostupnosti. Pokud je odolnost vaše nejvyšší prioritou, rozšíří se vaše instance napříč několika zónami dostupnosti (jedna skupina umístění s jedním okolím nemůže zahrnovat zóny).