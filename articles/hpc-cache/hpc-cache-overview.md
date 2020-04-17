---
title: Přehled mezipaměti HPC Azure
description: Popisuje Azure HPC Cache, řešení akcelerátoru přístupu k souborům pro vysoce výkonné výpočty.
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 084c33874b474fc1789df93e088d3cec4263eac9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536637"
---
# <a name="what-is-azure-hpc-cache"></a>Co je Azure HPC Cache?

Azure HPC Cache urychluje přístup k vašim datům pro úlohy vysoce výkonnévýpočetní techniky (HPC). Ukládáním souborů do mezipaměti v Azure přináší Azure HPC Cache škálovatelnost cloud computingu do vašeho stávajícího pracovního postupu. Tuto službu lze použít i pro pracovní postupy, kde jsou vaše data uložena přes propojení WAN, například v prostředí úložiště připojeného k síti místního datového centra (NAS).

Azure HPC Cache se snadno spouští a monitoruje z webu Azure Portal. Existující úložiště systému efs nebo nové kontejnery objektů Blob se může stát součástí jeho agregované obor názvů, což usnadňuje přístup klienta i v případě, že změníte cíl úložiště back-end.

## <a name="use-cases"></a>Případy použití

Azure HPC Cache zvyšuje produktivitu nejlépe pro pracovní postupy, jako jsou tyto:

* Pracovní postup přístupu k souborům s velkým úkolem pro čtení
* Data uložená v úložišti přístupném pro zabezpečení nfs, azure blob nebo obojí
* Výpočetní farmy s až 75 000 procesorovými jádry

Azure HPC Cache lze přidat do široké škály pracovních postupů v mnoha odvětvích. Každý systém, kde velký počet počítačů potřebují přístup k sadě souborů ve velkém měřítku a s nízkou latencí budou mít prospěch z této služby. Níže uvedené části udávají konkrétní příklady.

### <a name="visual-effects-vfx-rendering"></a>Vykreslování vizuálních efektů (VFX)

V médiích a zábavě může Azure HPC Cache urychlit přístup k datům pro časově kritické vykreslovací projekty. Pracovní postupy vykreslování virtuálních efektů často vyžadují zpracování na poslední chvíli velkým počtem výpočetních uzlů. Data pro tyto pracovní postupy se obvykle nacházejí v místním prostředí NAS. Azure HPC Cache může ukládat tato data souborů do mezipaměti v cloudu, aby se snížila latence a zvýšila flexibilita pro vykreslování na vyžádání.

### <a name="life-sciences"></a>Lékařské technologie

Mnoho pracovních postupů věd o živé přírodě může těžit z ukládání souborů s horizontálním navýšením kapacity.

Výzkumný ústav, který chce přenést své pracovní postupy genomické analýzy do Azure, je může snadno přesunout pomocí Azure HPC Cache. Vzhledem k tomu, že mezipaměť poskytuje přístup k souborům POSIX, nejsou k spuštění stávajícího pracovního postupu klienta v cloudu potřeba žádné změny na straně klienta.

Azure HPC Cache lze také využít ke zlepšení efektivity úloh, jako je sekundární analýza, farmakologická simulace nebo analýza obrázků řízená umělou školou.

### <a name="financial-services-analytics"></a>Analýza finančních služeb

Nasazení mezipaměti HPC Azure může pomoci urychlit výpočty kvantitativní analýzy, úlohy analýzy rizik a simulace Monte Carlo, aby společnosti poskytující finanční služby poskytly lepší přehled o strategických rozhodnutích.

## <a name="region-availability"></a>Dostupnost v oblastech

Azure HPC Cache je k dispozici v těchto oblastech Azure:

| Severní Amerika      | Evropa         | Asie            | Austrálie      |
|--------------------|----------------|-----------------|----------------|
| USA – východ            | Severní Evropa   | Jižní Korea – střed   | Austrálie – východ |
| USA – východ 2          | Západní Evropa    | Jihovýchodní Asie  |               |
| USA – středojih | | | |
| USA – západ 2        | | | |

[Funkce klíčů spravovaných zákazníkem](customer-keys.md) je podporována pouze v těchto oblastech:

* USA – východ
* USA – středojih
* USA – západ 2

Nejnovější informace o dostupnosti najdete na [stránce produktu Azure HPC Cache.](https://azure.microsoft.com/services/hpc-cache)

## <a name="next-steps"></a>Další kroky

* Další informace o jeho možnostech najdete na [stránce produktu Azure HPC Cache.](https://azure.microsoft.com/services/hpc-cache)
* Informace o [požadavcích](hpc-cache-prereqs.md) na produkty
* [Vytvoření mezipaměti Azure HPC](hpc-cache-create.md) z webu Azure Portal
