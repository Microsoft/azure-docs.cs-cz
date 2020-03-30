---
title: Microsoft Azure FXT Edge Filer – přehled
description: Popisuje mezipaměť hybridního úložiště Azure FXT Edge Filer, aktivní řešení akcelerátoru archivu a přístupu k souborům pro vysoce výkonné výpočty.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254842"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co je mezipaměť hybridního úložiště Azure FXT Edge Filer?

Azure FXT Edge Filer je hybridní úložiště ukládání do mezipaměti zařízení, které poskytuje rychlý přístup k souborům a aktivní archiv pro vysoce výkonné výpočetní úlohy (HPC).

Pracuje s více zdroji dat, ať už jsou uloženy v místním datovém centru, vzdáleně nebo v cloudu. Azure FXT Edge Filer může poskytnout jednotný obor názvů pro data v různých úložných systémech.

Tři nebo více hardwarových zařízení FXT Edge Filer spolupracují jako clusterovaný souborový systém, který poskytuje mezipaměť. Podrobnosti o zakoupení požadovaného hardwaru získáte od zástupce společnosti Microsoft. 

Další informace najdete v informacích o produktu a datovém listu v [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Případy použití

Azure FXT Edge Filer zvyšuje produktivitu nejlépe pro pracovní postupy, jako jsou tyto:

* Pracovní postup přístupu k souborům s velkým úkolem pro čtení 
* Protokoly NFSv3 nebo SMB2
* Výpočetní farmy s 1000 až 100 000 procesorovými jádry

### <a name="nas-optimization-and-scaling"></a>Optimalizace a škálování NAS

Ke mezipaměti Azure FXT Edge Filer můžete použít k hladkému přístupu ke stávajícím systémům NetApp a Dell EMC Isilon NAS. Můžete také přidat Azure Blob nebo jiné cloudové úložiště poskytovat škálovatelnost bez nutnosti přepracovat procesy přístupu k datům na straně klienta. 

### <a name="wan-caching"></a>Ukládání do mezipaměti sítě WAN

Azure FXT Edge Filer lze použít k podpoře rychlého přístupu k souborům od power uživatelů, když data, která potřebují, jsou uložena jinde. Poskytněte přístup při zachování zálohovacích a dalších systémů pro správu dat v centralizovaném datovém centru. 

### <a name="active-archive-in-azure-blob"></a>Aktivní archivace v objektu Blob Azure

Rozšiřte své datové centrum do cloudového úložiště s Azure FXT Edge Filer jako přístupový bod. 

## <a name="features"></a>Funkce 

K dispozici jsou dva modely hardwaru. 

| Model | Arménský | NVMe SSD | Síťové porty | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |


## <a name="next-steps"></a>Další kroky

* Pokračujte ve správě o Azure FXT Edge Filer načtením [specifikací](fxt-specs.md) nebo [kurzu instalace](fxt-install.md).
* Přečtěte si, jak zakoupit Azure FXT Edge Filer na [stránce produktu Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/).