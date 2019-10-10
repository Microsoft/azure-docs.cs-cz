---
title: Přehled Azure HPC cache Preview
description: Popisuje řešení Azure HPC cache, což je přístupové akcelerátory pro přístup k souborům pro vysoce výkonné výpočetní prostředí.
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: bfbbcd2d7d1bb44e260bedda54ca38ed6860ea67
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254480"
---
# <a name="what-is-azure-hpc-cache-preview"></a>Co je mezipaměť prostředí Azure HPC? Tisk

Azure HPC ukládá přístup k vašim datům pro úlohy využívající vysoce výkonné výpočetní prostředí (HPC). Díky ukládání do mezipaměti souborů v Azure přináší mezipaměť HPC Azure škálovatelnost cloud computingu do vašeho existujícího pracovního postupu. Tato služba se dá použít i pro pracovní postupy, ve kterých se vaše data ukládají přes odkazy sítě WAN, jako třeba v prostředí místního síťového úložiště (NAS) místního datacentra.

Mezipaměť prostředí Azure HPC se snadno spouští a monitoruje z Azure Portal. Existující úložiště NFS nebo nové kontejnery objektů BLOB se můžou stát součástí agregovaného oboru názvů, který dá klientský přístup jednoduchý, i když změníte cíl úložiště back-endu.

## <a name="use-cases"></a>Případy použití

Azure HPC cache vylepšuje nejlepší produktivitu pro pracovní postupy, jako jsou tyto:

* Pracovní postup pro čtení a těžký přístup k souborům
* Data uložená v úložišti, které je přístupné pro systém souborů NFS, Azure Blob nebo obojí
* Výpočetní farmy až 75 000 PROCESORových jader

Mezipaměť prostředí Azure HPC se dá přidat do široké škály pracovních postupů napříč mnoha průmyslovými odvětvími. Pro všechny systémy, ve kterých velký počet počítačů potřebuje přístup k sadě souborů se škálováním a s nízkou latencí, bude výhodná tato služba. Následující části obsahují konkrétní příklady.

### <a name="visual-effects-vfx-rendering"></a>Vykreslování vizuálních efektů (VFX)

V případě médií a zábavy může mezipaměť prostředí Azure HPC zrychlit přístup k datům pro projekty vykreslování kritické pro čas. Pracovní postupy vykreslování VFX často vyžadují poslední minuty zpracování velkým počtem výpočetních uzlů. Data pro tyto pracovní postupy se většinou nacházejí v místním prostředí NAS. Mezipaměť HPC Azure může ukládat do mezipaměti tato data souborů v cloudu, aby se snížila latence a vylepšila se flexibilita při vykreslování na vyžádání.

### <a name="life-sciences"></a>Vědy o života

Mnohé pracovní postupy pro biologickou škálu můžou využívat ukládání souborů do mezipaměti se škálováním na více instancí.

Výzkumný ústav, který chce přenést své pracovní postupy analýzy genomiky do Azure, je může snadno přesunout pomocí mezipaměti HPC Azure. Vzhledem k tomu, že mezipaměť poskytuje přístup k souborům POSIX, nejsou pro spuštění stávajícího pracovního postupu klienta v cloudu potřeba žádné změny na straně klienta.

Mezipaměť prostředí Azure HPC je také možné využít ke zvýšení efektivity v úlohách, jako je sekundární analýza, farmakologická simulace nebo analýza obrázků řízených AI.

### <a name="financial-services-analytics"></a>Analýzy finančních služeb

Nasazení mezipaměti HPC Azure vám může pomoci zrychlit kvantitativní výpočty analýz, úlohy analýzy rizik a Monte Carlo simulace, které společnosti poskytují lepší přehled o strategických rozhodnutích.

## <a name="region-availability"></a>Dostupnost oblasti

Mezipaměť prostředí Azure HPC je dostupná v těchto oblastech Azure:

* Východní USA
* Východní USA 2
* Severní Evropa
* Západní Evropa
* Jihovýchodní Asie
* Západní USA 2

Nejnovější informace o dostupnosti najdete na [stránce produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .

## <a name="preview-availability"></a>Dostupnost verze Preview

Služba Azure HPC cache ve verzi Public Preview je omezená, aby se zajistila kvalita služeb. Vyžádejte si přístup vyplněním [tohoto formuláře](https://aka.ms/onboard-hpc-cache). Po přidání předplatného do seznamu přístupu můžete vytvořit mezipaměť testů.

## <a name="next-steps"></a>Další kroky

* Další informace o jeho funkcích najdete na [stránce produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .
* Další informace o [požadavcích](hpc-cache-prereqs.md) produktu
* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
