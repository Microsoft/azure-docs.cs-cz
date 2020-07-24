---
title: Přehled mezipaměti HPC Azure
description: Popisuje řešení Azure HPC cache, což je přístupové akcelerátory pro přístup k souborům pro vysoce výkonné výpočetní prostředí.
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 07/16/2020
ms.author: v-erkel
ms.custom: references_regions
ms.openlocfilehash: 8c8223872ec3ef1d875e10ead8f327db01a924c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053684"
---
# <a name="what-is-azure-hpc-cache"></a>Co je Azure HPC Cache?

Azure HPC ukládá přístup k vašim datům pro úlohy využívající vysoce výkonné výpočetní prostředí (HPC). Díky ukládání do mezipaměti souborů v Azure přináší mezipaměť HPC Azure škálovatelnost cloud computingu do vašeho existujícího pracovního postupu. Tato služba se dá použít i pro pracovní postupy, ve kterých se vaše data ukládají přes odkazy sítě WAN, jako třeba v prostředí místního síťového úložiště (NAS) místního datacentra.

Mezipaměť prostředí Azure HPC se snadno spouští a monitoruje z Azure Portal. Existující úložiště NFS nebo nové kontejnery objektů BLOB se můžou stát součástí agregovaného oboru názvů, který dá klientský přístup jednoduchý, i když změníte cíl úložiště back-endu.

## <a name="overview-video"></a>Přehled – video

[![Miniatura videa: Přehled Azure HPC cache – kliknutím můžete navštívit stránku video.](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Klikněte na obrázek výše a podívejte se na [stručný přehled mezipaměti HPC Azure](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

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

Výzkumný ústav, který chce přenést své pracovní postupy analýzy genomiky do Azure, je může snadno přesunout pomocí mezipaměti HPC Azure. Vzhledem k tomu, že mezipaměť poskytuje přístup k souborům POSIX, nejsou pro spuštění stávajícího pracovního postupu klienta v cloudu potřeba žádné změny na straně klienta.

Mezipaměť prostředí Azure HPC je také možné využít ke zvýšení efektivity v úlohách, jako je sekundární analýza, farmakologická simulace nebo analýza obrázků řízených AI.

### <a name="financial-services-analytics"></a>Analýza finančních služeb

Nasazení mezipaměti HPC Azure vám může pomoci zrychlit kvantitativní výpočty analýz, úlohy analýzy rizik a Monte Carlo simulace, které společnosti poskytují lepší přehled o strategických rozhodnutích.

## <a name="region-availability"></a>Dostupnost v oblastech

Navštivte stránku [globální infrastruktura Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?products=hpc-cache) a zjistěte, kde je mezipaměť HPC Azure k dispozici.

[Funkce klíčů spravovaných zákazníkem](customer-keys.md) je podporovaná jenom v těchto oblastech Azure:

* East US
* Středojižní USA
* Západní USA 2
* Západní Evropa
* USA (Gov) – Arizona
* USA (Gov) – Virginia

## <a name="service-availability"></a>Dostupnost služby

Musíte požádat o přístup pro každé předplatné, které budete používat s mezipamětí Azure HPC. Toto omezení pomáhá zajistit kvalitu služby.

Vyžádejte si přístup vyplněním [tohoto formuláře](https://aka.ms/onboard-hpc-cache). Po přidání předplatného do seznamu přístupu můžete vytvořit mezipaměti.

## <a name="next-steps"></a>Další kroky

* Další informace o jeho funkcích najdete na [stránce produktu Azure HPC cache](https://azure.microsoft.com/services/hpc-cache) .
* Další informace o [požadavcích](hpc-cache-prerequisites.md) produktu
* [Vytvoření mezipaměti prostředí Azure HPC](hpc-cache-create.md) z Azure Portal
