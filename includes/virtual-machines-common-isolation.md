---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: e22c2b7cb561e30e84ea5ede5481fbdc35be8cdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514943"
---
Azure COMPUTE nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka. Izolované velikosti fungují v reálném čase a pracují na konkrétní generaci hardwaru a při vyřazení hardwaru budou zastaralé.

Izolované velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysokou úroveň izolace od úloh jiných zákazníků z důvodů, které zahrnují dodržování předpisů a zákonné požadavky na schůzku.  Využitím izolované velikosti zaručujete, že váš virtuální počítač bude jediným operačním systémem, který běží na konkrétní instanci serveru. 


Vzhledem k velkému počtu virtuálních počítačů izolované velikosti můžou zákazníci rozdělit prostředky těchto virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Mezi aktuální nabídky izolovaných virtuálních počítačů patří:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Velikost izolovaného virtuálního počítače má omezený životnost hardwaru. Podrobnosti najdete níže.

## <a name="deprecation-of-isolated-vm-sizes"></a>Vyřazení velikostí izolovaného virtuálního počítače

Velikost izolovaného virtuálního počítače má omezený životnost hardwaru. Azure bude vydávat připomenutí 12 měsíců předem od oficiálního data vyřazení velikostí a poskytne vám aktualizovanou izolovanou nabídku pro váš aspekt.

| Velikost | Datum vyřazení izolace | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15. května 2020 |
| Standard_D15_v2<sup>1</sup>  | 15. května 2020 |

<sup>1</sup>  podrobnosti o Standard_DS15_v2 a Standard_D15_v2 programu pro vyřazení z izolaci najdete v tématu Nejčastější dotazy.


## <a name="faq"></a>Časté otázky
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>Otázka: má být vyřazení z provozu nebo pouze funkce izolace?
Odpověď **: Pokud** velikost virtuálního počítače nemá dolní index "i", bude vyřazena pouze funkce izolace. Pokud není potřeba izolaci, nebude provedena žádná akce a virtuální počítač bude i nadále fungovat podle očekávání. Mezi příklady patří Standard_DS15_v2, Standard_D15_v2, Standard_M128ms atd. Pokud velikost virtuálního počítače zahrnuje i "i" dolní index, je tato velikost vyřazení.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Otázka: dojde k výpadku, když je můj virtuální počítač na neizolovaný hardware?
Odpověď **: Pokud** nepotřebujete izolaci, není nutné provádět žádnou akci a nedojde k žádnému výpadku.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Otázka: je pro přechod na neizolovaný virtuální počítač k dispozici cenová Delta?
Odpověď **: ne**

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Otázka: kdy jsou jiné izolované velikosti vyřazení?
Odpověď: zadáte připomenutí 12 měsíců předem od oficiálního vyřazení izolované velikosti.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Otázka: jsem zákazníkem Service Fabric Azure, který se spoléhá na úroveň odolnosti stříbrného nebo zlata. Má tato změna vliv?
Odpověď **: ne**. Záruky poskytované [úrovněmi odolnosti](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric budou i nadále fungovat i po této změně. Pokud požadujete izolaci fyzického hardwaru z jiných důvodů, možná budete muset provést jednu z výše uvedených akcí. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Otázka: Jaké jsou milníky pro D15_v2 nebo DS15_v2 vyřazení z izolaci? 
**A**: 
 
| Date (Datum) | Akce |
|---|---| 
| 18. listopadu 2019 | Dostupnost D/DS15i_v2 (PAYG, 1-year) | 
| 14. května 2020 | Poslední den nákupu D/DS15i_v2 1-Year | 
| 15. května 2020 | Odebrání záruky izolace D/DS15_v2 | 
| 15. května 2021 | Vyřazení D/DS15i_v2 (všichni zákazníci s výjimkou zakoupených 3 DS15_v2 let a více než 18. listopadu 2019)| 
| 17. listopadu 2022 | Vyřazení D/DS15i_v2, když se dokončí 3 roky služby RIs (pro zákazníky, kteří koupili 3 roky rezervované instance u D/DS15_v2 do 18. listopadu 2019) |

## <a name="next-steps"></a>Další kroky

Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
