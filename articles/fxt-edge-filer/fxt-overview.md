---
title: FXT Edge souborového – přehled Microsoft Azure
description: Popisuje řešení Azure FXT Edge souborového pro hybridní úložiště, aktivní archivní a přístupové akcelerátory pro přístup k souborům pro vysoce výkonné výpočty.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218778"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Co je Azure FXT Edge – mezipaměť hybridního úložiště souborového?

Azure FXT Edge souborového je řešení hybridního ukládání do mezipaměti, které poskytuje rychlý přístup k souborům a aktivní archiv pro úlohy s vysokým výkonem výpočetního prostředí (HPC).

Funguje s více zdroji dat, ať už jsou uložené v místním datovém centru, vzdáleně nebo v cloudu. Azure FXT Edge souborového může poskytovat sjednocený obor názvů pro data v různých systémech úložišť.

Tři nebo více hardwarových zařízení FXT Edge souborového společně spolupracuje jako clusterovaný systém souborů pro zajištění mezipaměti. Podrobnosti o nákupu požadovaného hardwaru získáte od zástupce společnosti Microsoft.

Pokud se chcete dozvědět víc, přečtěte si informace o produktu a datový list na [Azure FXT Edge souborového](https://azure.microsoft.com/services/fxt-edge-filer/).

## <a name="use-cases"></a>Případy použití

Azure FXT Edge souborového vylepšuje produktivitu pro pracovní postupy, jako jsou tyto:

* Pracovní postup pro čtení a těžký přístup k souborům
* Protokoly NFSv3 nebo SMB2
* Výpočetní farmy 1000 až 100 000 PROCESORových jader

### <a name="nas-optimization-and-scaling"></a>Optimalizace a škálování pro servery NAS

Mezipaměť souborového pro Azure FXT Edge můžete použít k zajištění hladkého přístupu k existujícím systémům NetApp a Dell EMC Isilon NAS. Můžete taky přidat Azure Blob nebo jiné cloudové úložiště, abyste zajistili škálovatelnost bez nutnosti přepracovat procesy přístupu k datům na straně klienta.

### <a name="wan-caching"></a>Ukládání do mezipaměti sítě WAN

Azure FXT Edge souborového se dá použít k podpoře rychlého přístupu k souborům od uživatelů v případě, že data, která potřebují, jsou uložená jinde. Poskytněte přístup a zajistěte v centralizovaném datovém centru zálohování a další systémy správy dat.

### <a name="active-archive-in-azure-blob"></a>Aktivní archiv v objektu blob Azure

Rozšiřte své datové centrum do cloudového úložiště s využitím Azure FXT Edge souborového jako přístupového bodu.

## <a name="features"></a>Funkce

K dispozici jsou dva hardwarové modely.

| Modelování | DRAM | NVMe SSD | Síťové porty |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 × 25 GB/10Gb + 2 × 1 GB |
| FXT 6400 | 768 GB | 12,8 TB | 6 × 25 GB/10Gb + 2 × 1 GB |

## <a name="next-steps"></a>Další kroky

* Další informace o Azure FXT Edge souborového najdete v kurzu [specifikace](fxt-specs.md) nebo [instalace](fxt-install.md).
* Přečtěte si, jak zakoupit Azure FXT Edge souborového na [stránce produktu Azure FXT Edge souborového](https://azure.microsoft.com/services/fxt-edge-filer/).
