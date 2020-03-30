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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73171089"
---
Umístění virtuálních zařízení v jedné oblasti snižuje fyzickou vzdálenost mezi instancemi. Jejich umístění do jedné zóny dostupnosti je také fyzicky sblíží. Však jako stopa Azure roste, jedna zóna dostupnosti může span více fyzických datových center, což může mít za následek latence sítě ovlivňující vaši aplikaci. 

Chcete-li získat virtuální chod co nejblíže a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci skupiny umístění bez kontaktní chod.

Skupina umístění bezkontaktní komunikace je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěny blízko sebe. Skupiny umístění bezkontaktní komunikace jsou užitečné pro úlohy, kde je požadavek nízké latence.


- Nízká latence mezi samostatnými virtuálními servery.
- Nízká latence mezi virtuálními počítači v jedné sadě dostupnosti nebo škálovací sadě virtuálních strojů. 
- Nízká latence mezi samostatnými virtuálními počítači, virtuálními počítači ve více sadách dostupnosti nebo více škálovacími sadami. Můžete mít více výpočetních prostředků v jedné skupině umístění, které sdružují vícevrstvou aplikaci. 
- Nízká latence mezi více aplikačními vrstvami pomocí různých typů hardwaru. Například spuštění back-endu pomocí řady M v sadě dostupnosti a front-end u instance řady D v škálovací sadě v jedné skupině umístění bezkontaktní komunikace.


![Grafika pro skupiny umístění bezkontaktní blízkosti](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Použití skupin umístění bezkontaktní komunikace 

Skupina umístění bezkontaktní komunikace je nový typ prostředku v Azure. Před použitím s jinými prostředky je třeba jej vytvořit. Po vytvoření ji lze použít s virtuálními počítači, sadami dostupnosti nebo škálovacími sadami virtuálních počítačů. Skupinu umístění bezkontaktní komunikace určíte při vytváření výpočetních prostředků poskytujících ID skupiny umístění bezkontaktní komunikace. 

Existující prostředek můžete také přesunout do skupiny umístění bez kontaktní ch odst. Při přesouvání prostředku do skupiny umístění bezkontaktní komunikace byste měli nejprve zastavit (navrátit) datový zdroj, protože bude potenciálně přeobsazen do jiného datového centra v oblasti, aby bylo splněno omezení společného umístění. 

V případě sad dostupnosti a škálovacích sad virtuálních počítačů byste měli nastavit skupinu umístění bezkontaktní komunikace na úrovni prostředků, nikoli na úrovni jednotlivých virtuálních počítačů. 

Skupina umístění bez kontaktní místa je omezení společného umístění, nikoli připnuté mechanismus. Je připnutý do konkrétního datového centra s nasazením prvního prostředku, který jej používá. Jakmile jsou všechny prostředky používající skupinu umístění bezkontaktní komunikace zastaveny (s ambulancí) nebo odstraněny, již se nepřipnou. Proto při použití skupiny umístění bezkontaktní komunikace s více řadami virtuálních počítačů, je důležité zadat všechny požadované typy předem v šabloně, pokud je to možné, nebo postupujte podle pořadí nasazení, které zlepší vaše šance na úspěšné nasazení. Pokud se nasazení nezdaří, restartujte nasazení s velikostí virtuálního počítače, která se nezdařila jako první velikost, která má být nasazena.


## <a name="best-practices"></a>Osvědčené postupy 
- Pro nejnižší latenci použijte skupiny umístění bezkontaktní komunikace spolu se zrychleným vytvářením sítí. Další informace naleznete [v tématu Vytvoření virtuálního počítače s Linuxem se zrychlenou sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo Vytvoření [virtuálního počítače se systémem Windows se zrychlenou sítí](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Nasaďte všechny velikosti virtuálních počítače v jedné šabloně. Chcete-li se vyhnout přistání na hardwaru, který nepodporuje všechny skuténky virtuálních zařízení a velikosti, které požadujete, zahrnout všechny aplikační vrstvy v jedné šabloně tak, aby všechny budou nasazeny ve stejnou dobu.
- Pokud skriptujete nasazení pomocí prostředí PowerShell, CLI nebo sady SDK, může se zobrazit chyba `OverconstrainedAllocationRequest`přidělení . V takovém případě byste měli zastavit/navrátit všechny existující virtuální počítače a změnit pořadí ve skriptu nasazení tak, aby začínala s položkou nebo velikostmi virtuálních virtuálních stránek, které se nezdařily. 
- Při opakovaném použití existující skupiny umístění, ze kterých byly odstraněny virtuální chod, počkejte na úplné dokončení odstranění před přidáním virtuálních hraček do něj.
- Pokud latence je vaše první priorita, vložte virtuální počítače do skupiny umístění bez kontaktní místo a celé řešení v zóně dostupnosti. Pokud je však vaší nejvyšší prioritou odolnost proti chybám, rozdělte instance do více zón dostupnosti (jedna skupina umístění bez kontaktních míst nemůže přesáhnout zóny).