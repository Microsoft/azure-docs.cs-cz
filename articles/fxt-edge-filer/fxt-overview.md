---
title: Přehled Microsoft Azure FXT hrany vyfiltrovat
description: Popisuje mezipaměť hybridní úložiště Azure FXT hrany vyfiltrovat, aktivní archiv a akcelerátor řešení přístupu k souboru pro vysokovýkonné výpočetní prostředí
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 58d4a2a52757b6ace1059fcccf379df3de5fd75c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542908"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co je Azure FXT hrany Filer hybridní úložiště mezipaměti?

Azure FXT hrany Filer je hybridní úložiště ukládání do mezipaměti zařízení, která poskytuje rychlejší přístup a aktivní archivu pro úlohy vysokovýkonného výpočetního prostředí (HPC).

Pracuje s několika zdroji dat, ať už uložené v místním datovém centru, vzdáleně, nebo v cloudu. Vyfiltrovat Azure FXT hrany může poskytnout jednotný obor názvů pro data v různých úložných systémů.

Tři nebo více FXT hrany Filer hardwarových zařízení společně pracují jako clusterovaný systém souborů k poskytování mezipaměti. Podrobnosti o nákupu požadovaný hardware kontaktujte zástupce Microsoftu. 

Další informace, přečtěte si list informací a dat produktu na [Azure FXT hrany Filer](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Případy použití

Vyfiltrovat Edge FXT Azure zvyšuje produktivitu, které jsou nejvhodnější pro pracovní postupy, jako jsou tyto:

* Pracovní postup přístup náročná na výkon pro čtení souboru 
* Protokoly NFSv3 nebo protokolu SMB2
* Výpočetní farmy 1 000 až 100 000 Procesorových jader

### <a name="nas-optimization-and-scaling"></a>Optimalizace NAS a škálování

Můžete mezipaměti Azure FXT hrany Filer plynulé přístup k existujícím systémům NetApp a Dell EMC Isilon NAS. Také můžete přidat objektů Blob v Azure nebo jiné cloudové úložiště poskytují škálovatelnost bez nutnosti přepracovat procesů přístupu dat na straně klienta. 

### <a name="wan-caching"></a>Síť WAN ukládání do mezipaměti

Azure FXT hrany filtr lze použít pro podporu rychlejší přístup z zkušeným uživatelům při data, která potřebují je uložená na jiném místě. Poskytnutí přístupu při zachování zálohování a jinými systémy pro správu dat v centralizované datového centra. 

### <a name="active-archive-in-azure-blob"></a>Aktivní archivní úrovni v Azure Blob

Rozšíření vašeho datového centra do cloudového úložiště pomocí Azure FXT hrany vyfiltrovat jako přístupový bod. 

## <a name="features"></a>Funkce 

Jsou k dispozici dva modely hardwaru. 

| Model | DRAM | NVMe SSD | Síťové porty | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>Další postup

* Pokračujte ve čtení o Azure FXT hrany Filer načtením [specifikace](fxt-specs.md) nebo [kurz instalace](fxt-install.md).
* Zjistěte, jak koupit Azure FXT hrany vyfiltrovat na [stránce produktu Azure FXT hrany Filer](https://azure.microsoft.com/services/fxt-edge-filer/).