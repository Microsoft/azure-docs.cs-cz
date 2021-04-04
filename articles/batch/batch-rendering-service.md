---
title: Přehled vykreslování
description: Seznámení s používáním Azure pro vykreslování a přehled možností vykreslování Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232110"
---
# <a name="rendering-using-azure"></a>Vykreslování s využitím Azure

Vykreslování je proces přebírání 3D modelů a jejich převod na 2D obrázky. 3D soubory scény jsou vytvořené v aplikacích, jako je například Autodesk 3ds Max, Autodesk Maya a Blend.  Vykreslování aplikací, jako jsou Autodesk Maya, Autodesk Arnold, chaos Group V-Ray a cykly Blendu, vytváří 2D obrázky.  Někdy se v souborech scény vytvoří jeden obrázek. Je však běžné modelování a vykreslování více imagí a jejich kombinování do animace.

Úlohy vykreslování se silně používají pro zvláštní efekty (VFX) v mediálním a zábavním odvětví. Vykreslování se používá i v mnoha dalších odvětvích, jako je reklama, maloobchodní prodej, ropný a plynárenský průmysl nebo výroba.

Proces vykreslování je výpočetně náročný; můžete vytvářet spoustu snímků a obrázků a každý z nich může vykreslování trvat spoustou hodin.  Vykreslování je proto ideální úlohou dávkového zpracování, která může využít Azure k paralelnímu spouštění mnoha vykreslování a využití široké škály hardwaru, včetně GPU.

## <a name="why-use-azure-for-rendering"></a>Proč používat Azure pro vykreslování?

Z mnoha důvodů je vykreslování pro Azure naprosto uzpůsobené jako zatížení:

* Úlohy vykreslování lze rozdělit do mnoha částí, které lze spustit paralelně pomocí několika virtuálních počítačů:
  * Animace se skládají z mnoha snímků a každý rámec lze vykreslit paralelně.  Čím víc virtuálních počítačů je dostupných pro zpracování každého snímku, tím rychleji se vytvoří všechny snímky a animace.
  * Některé vykreslovací software umožňuje rozdělit jednotlivé snímky do více částí, jako jsou například dlaždice nebo řezy.  Každý kus se dá vykreslit samostatně a pak se sloučí do finální image, až se všechny díly dokončí.  Čím více virtuálních počítačů je k dispozici, tím rychlejší je možné vykreslení rámce.
* Vykreslování projektů může vyžadovat obrovský rozsah:
  * Jednotlivé snímky můžou být složité a vyžadují mnoho hodin pro vykreslování, i na špičkovém hardwaru. animace se můžou skládat ze stovky tisíc snímků.  K vykreslování vysoce kvalitních animací v rozumné době je potřeba velké množství výpočetních prostředků.  V některých případech se více než 100 000 jader používá k vykreslování tisíců snímků paralelně.
* Projekty vykreslování jsou založené na projektu a vyžadují proměnlivé množství výpočetních prostředků:
  * Přidělte výpočetní a úložnou kapacitu v případě potřeby, škálovat ji nahoru nebo dolů v závislosti na zatížení během projektu a po dokončení projektu ho odeberte.
  * Platíte za kapacitu při přidělení, ale Neplaťte za ni, pokud není žádné zatížení, například mezi projekty.
  * Stravování pro shluky kvůli neočekávaným změnám škálování vyšší, pokud v projektu nedošlo k neočekávaným změnám, a tyto změny je potřeba zpracovat v těsném plánu.
* Vyberte si z nejrůznějších hardwarových zařízení podle aplikace, úlohy a časového rámce:
  * V Azure je dostupná celá síť, kterou je možné přidělit a spravovat pomocí služby Batch.
  * V závislosti na projektu může požadavek vycházet z hlediska nejvyšší ceny a výkonu nebo nejlepšího celkového výkonu.  Různé scény nebo vykreslování aplikací budou mít různé požadavky na paměť.  Některé aplikace pro vykreslování můžou využít GPU k dosažení nejlepšího výkonu nebo určitých funkcí. 
* Virtuální počítače s nízkou prioritou nebo [virtuální počítače](https://azure.microsoft.com/pricing/spot/) snižují náklady:
  * Virtuální počítače s nízkou prioritou jsou dostupné pro velkou slevu ve srovnání se standardními virtuálními počítači a jsou vhodné pro některé typy úloh.
  
## <a name="existing-on-premises-rendering-environment"></a>Existující místní vykreslovací prostředí

Nejběžnějším případem je, že by existovala existující místní farma vykreslování, kterou spravuje aplikace pro správu vykreslování, jako je PipelineFX Qube, Královská Render, Thinkbox konečný termín nebo vlastní aplikace.  Požadavek slouží k prodloužení kapacity místní vykreslovací farmy pomocí virtuálních počítačů Azure.

Infrastruktura a služby Azure slouží k vytvoření hybridního prostředí, ve kterém se Azure používá k doplnění místní kapacity. Například:

* Použijte [Virtual Network](../virtual-network/virtual-networks-overview.md) k umístění prostředků Azure ve stejné síti jako místní farma vykreslování.
* Pomocí [avere vFXT pro Azure](../avere-vfxt/avere-vfxt-overview.md) nebo [Azure HPC cache](../hpc-cache/hpc-cache-overview.md) můžete ukládat zdrojové soubory do mezipaměti v Azure, abyste snížili využití šířky pásma a latenci a maximalizujete výkon.
* Ujistěte se, že stávající licenční server je ve virtuální síti, a Zakupte další požadavky na Azure, které jsou potřebné pro službu stravování.

## <a name="no-existing-render-farm"></a>Žádná existující farma vykreslování

Klientské pracovní stanice mohou provádět vykreslování, ale zatížení vykreslování se zvyšuje a trvá příliš dlouho, aby používala kapacitu pracovní stanice.

K dispozici jsou dvě hlavní možnosti:

* Nasazení místního správce vykreslování, jako je Královská rendering, a konfigurace hybridního prostředí pro použití Azure, pokud je potřeba další kapacita nebo výkon. Správce vykreslování je speciálně přizpůsobený pro úlohy vykreslování a bude obsahovat moduly plug-in pro oblíbené klientské aplikace, které umožňují snadné odeslání úloh vykreslování.

* Vlastní řešení, které používá Azure Batch k přidělení a správě výpočetní kapacity a k poskytování plánování úloh pro spuštění úloh vykreslování.

## <a name="next-steps"></a>Další kroky

 Naučte se [používat infrastrukturu a služby Azure k rozšiřování stávající místní farmy vykreslování](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

Přečtěte si další informace o [možnostech vykreslování Azure Batch](batch-rendering-functionality.md).
