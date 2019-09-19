---
title: Přehled mezipaměti HPC Azure
description: Popisuje řešení Azure HPC cache, což je přístupové akcelerátory pro přístup k souborům pro vysoce výkonné výpočetní prostředí.
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 77eca3ef58733a616705fdaac1d2880b5a990e8a
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036884"
---
# <a name="what-is-azure-hpc-cache"></a>Co je Azure HPC Cache?

Azure HPC ukládá přístup k vašim datům pro úlohy využívající vysoce výkonné výpočetní prostředí (HPC). Díky ukládání souborů do mezipaměti v Azure zajišťuje škálovatelnost cloud computingu, a to i pro pracovní postupy, ve kterých jsou vaše data uložená v sítích WAN, jako třeba v prostředí místního síťového úložiště (NAS) připojené k síti datacentra.

Mezipaměť prostředí Azure HPC se snadno spouští a monitoruje z Azure Portal. Existující úložiště NFS nebo nové kontejnery objektů BLOB se můžou stát součástí agregovaného oboru názvů, který dá klientský přístup jednoduchý, i když změníte cíl úložiště back-endu.

## <a name="use-cases"></a>Případy použití

Azure HPC cache vylepšuje nejlepší produktivitu pro pracovní postupy, jako jsou tyto:

* Pracovní postup pro čtení a těžký přístup k souborům
* Data uložená v úložišti, které je přístupné pro systém souborů NFS, Azure Blob nebo obojí
* Výpočetní farmy až 75 000 PROCESORových jader

Mezipaměť prostředí Azure HPC se dá přidat do široké škály pracovních postupů napříč mnoha průmyslovými odvětvími. Pro všechny systémy, ve kterých velký počet počítačů potřebuje přístup k sadě souborů se škálováním a s nízkou latencí, bude výhodná tato služba. Následující části obsahují konkrétní příklady.

### <a name="visual-effects-vfx-rendering"></a>Vykreslování vizuálních efektů (VFX)

V případě médií a zábavy může mezipaměť prostředí Azure HPC zrychlit přístup k datům pro projekty vykreslování kritické pro čas. Pracovní postupy vykreslování VFX často vyžadují poslední minuty zpracování velkým počtem výpočetních uzlů. Data pro tyto pracovní postupy se většinou nacházejí v místním prostředí NAS. Mezipaměť HPC Azure může ukládat do mezipaměti tato data souborů v cloudu, aby se snížila latence a vylepšila se flexibilita při vykreslování na vyžádání.

### <a name="life-sciences"></a>Lékařské technologie

Mnohé pracovní postupy pro biologickou škálu můžou využívat ukládání souborů do mezipaměti se škálováním na více instancí.

Výzkumný ústav, který chce přenést své pracovní postupy analýzy genomiky do Azure, je může snadno přesunout pomocí mezipaměti HPC Azure. Vzhledem k tomu, že mezipaměť poskytuje přístup k souborům POSIX, může spustit stávající pracovní postup na straně klienta v cloudu bez jakýchkoli změn.

Mezipaměť prostředí Azure HPC je také možné využít ke zvýšení efektivity v úlohách, jako je sekundární analýza, farmakologická simulace nebo analýza obrázků řízených AI.

### <a name="financial-services-analytics"></a>Analýza finančních služeb

Mezipaměť prostředí Azure HPC vám může pomoci zrychlit kvantitativní výpočty analýz, úlohy analýzy rizik a Monte Carlo simulace a poskytnout tak společnosti finanční služby lepší přehled o strategických rozhodnutích.

## <a name="region-availability"></a>Dostupnost v oblastech

Mezipaměť prostředí Azure HPC je dostupná v těchto oblastech Azure:

* East US
* Východní USA 2
* Severní Evropa
* Západní Evropa
* Jihovýchodní Asie
* USA – západ 2

Nejnovější informace o dostupnosti najdete na [stránce produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .

## <a name="next-steps"></a>Další postup

* Další informace o jeho funkcích najdete na [stránce produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .
* Další informace o [požadavcích](hpc-cache-prereqs.md) produktu
* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
