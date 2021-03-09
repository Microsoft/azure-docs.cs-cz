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
ms.openlocfilehash: 3d78441e56e23cf49b09073fdf88bef4b3434da9
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473833"
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
| Standard_DS15_v2 | 15. května 2020 |
| Standard_D15_v2  | 15. května 2020 |
| Standard_G5  | 15. února 2021 |
| Standard_GS5  | 15. února 2021 |
| Standard_E64i_v3  | 15. února 2021 |
| Standard_E64is_v3  | 15. února 2021 |


## <a name="faq"></a>Časté otázky
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>Otázka: má být vyřazení z provozu nebo pouze funkce izolace?
Odpověď **: v** současné době je vyřazena pouze funkce izolace velikosti virtuálního počítače. Zastaralé izolované velikosti budou nadále existovat v neizolovaném stavu. Pokud není potřeba izolaci, nebude provedena žádná akce a virtuální počítač bude i nadále fungovat podle očekávání.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Otázka: dojde k výpadku, když je můj virtuální počítač na neizolovaný hardware?
Odpověď **: Pokud** nepotřebujete izolaci, není nutné provádět žádnou akci a nedojde k žádnému výpadku. V opačném případě, pokud je potřeba izolace, bude naše oznámení zahrnovat doporučenou velikost nahrazení. Výběr velikosti nahrazení bude vyžadovat, aby naši zákazníci změnili velikost svých virtuálních počítačů.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Otázka: je pro přechod na neizolovaný virtuální počítač k dispozici cenová Delta?
Odpověď **: ne**

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Otázka: kdy jsou jiné izolované velikosti vyřazení?
Odpověď: zadáte připomenutí 12 měsíců předem od oficiálního vyřazení izolované velikosti. Naše nejnovější oznámení zahrnuje funkci izolace Standard_G5, Standard_GS5, Standard_E64i_v3 a Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Otázka: jsem zákazníkem Service Fabric Azure, který se spoléhá na úroveň odolnosti stříbrného nebo zlata. Má tato změna vliv?
Odpověď **: ne**. Záruky poskytované [úrovněmi odolnosti](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric budou i nadále fungovat i po této změně. Pokud požadujete izolaci fyzického hardwaru z jiných důvodů, možná budete muset provést jednu z výše uvedených akcí. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Otázka: Jaké jsou milníky pro D15_v2 nebo DS15_v2 vyřazení z izolaci? 
**A**: 
 
| Date (Datum) | Akce |
|---|---| 
| 15. května 2019<sup>1</sup> | Oznámení o vyřazení z izolaci D/DS15_v2| 
| 15. května 2020 | Odebrání záruky izolace D/DS15_v2| 

<sup>1</sup> stávající zákazník používající tyto velikosti dostane e-mail s oznámením s podrobnými pokyny k dalšímu postupu.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>Otázka: Jaké jsou milníky pro G5, Gs5, E64i_v3 a E64is_v3 vyřazení z izolaci? 
**A**: 
 
| Date (Datum) | Akce |
|---|---|
| 15. února 2020<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 oznámení o vyřazení izolace |
| 15. února 2021 | Odebrání záruky izolace/GS5/E64i_v3/E64is_v3 |

<sup>1</sup> stávající zákazník používající tyto velikosti dostane e-mail s oznámením s podrobnými pokyny k dalšímu postupu.  

## <a name="next-steps"></a>Další kroky

Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
