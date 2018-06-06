---
title: Azure soubory škálovatelnosti a cílech výkonnosti | Microsoft Docs
description: Další informace o škálovatelnosti a výkonu cíle pro soubory, Azure, včetně kapacitu, rychlost požadavků a omezení šířky pásma pro příchozí a odchozí.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: beb3e5caf8c8dce9b2ea06bbd0a2ea5a4e05a714
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738070"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure soubory škálovatelnosti a cílech výkonnosti
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes průmyslový standard protokolu SMB. Tento článek popisuje cíle škálovatelnost a výkon pro soubory Azure a synchronizace souborů Azure (Preview).

Škálovatelnost a výkon cíle, které jsou zde uvedeny jsou vyšší kategorie cíle, ale může mít vliv jiné proměnné ve vašem nasazení. Například propustnost pro soubor může také být omezen vaši dostupnou šířku pásma sítě, nikoli pouze servery, které hostují službu Azure Files. Důrazně doporučujeme testování vaší vzor používání a zjistit, zda škálovatelnost a výkon Azure Files splňují vaše požadavky. Můžeme také potvrdí na zvýšení těchto mezních hodnot v čase. Prosím neváhejte a sdělte nám svůj názor, buď v komentářích menší než nebo na [Azure soubory UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), o které omezení chcete nás zvýšit.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu úložiště Azure
Nadřazený prostředek pro sdílenou složku Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, které je možné k ukládání dat pomocí více služby úložiště, včetně souborů Azure. Další služby, které ukládají data v účtech úložiště jsou Azure Blob storage, Azure Queue storage a Azure Table storage. Následující cíle použít všechny služby úložiště, ukládání dat v účtu úložiště:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití účet úložiště z jiných služeb úložiště ovlivní vaše sdílené složky Azure ve vašem účtu úložiště. Například Pokud dostanete maximální úložnou kapacitu účet s úložištěm Azure Blob, nebudete moci vytvořit nové soubory na vaši sdílenou složku Azure, i když Azure sdílené složky je menší než maximální sdílená.

## <a name="azure-files-scale-targets"></a>Azure soubory měřítka cíle
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure měřítka cíle synchronizace souboru
S Azure souboru Sync mají Pokusili jsme co nejvíce návrh pro neomezenou využití, ale to není možné. Níže uvedená tabulka určuje hranice našich testech a cíle, které jsou ve skutečnosti pevných limitů:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu Azure synchronizace souboru
Vzhledem k tomu, že agent synchronizace souboru Azure běží na počítači s Windows serverem, která se připojuje k Azure sdílené složky, výkon efektivní synchronizace závisí na počtu faktorů ve vaší infrastruktuře: Windows Server a na konfiguraci základní disk šířku pásma sítě mezi serverem a Azure storage, velikost souboru, velikost celkový datové sady a aktivity na datovou sadu. Vzhledem k tomu, že synchronizace souboru Azure pracuje na úrovni souborů, výkonové charakteristiky řešení Azure souboru synchronizačních lépe měří v počet objektů (soubory a adresáře) zpracovaných za sekundu. 
 
Výkon pro synchronizaci souborů Azure, je důležité ve dvou fázích:
1. **Počáteční jednorázové zřizování**: Optimalizace výkonu v počátečním zřizování, najdete v tématu [registrace se synchronizací souboru Azure](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) podrobnosti optimální nasazení.
2. **Probíhající synchronizace**: po dat je původně nasadí do sdílené složky Azure file, synchronizace souboru Azure udržuje několik koncových bodů synchronizované.

Při plánování nasazení pro každé z fází, níže jsou výsledky dodržovány při interním testování v systému konfigurace
| Konfigurace systému |  |
|-|-|
| Procesor | 64 virtuální jader s 64 L3 MiB |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS s RAID 10 s baterie zálohovaný mezipaměti |
| Síť | 1 GB/s sítě |
| Úloha | Souborový Server pro obecné použití|

| Počáteční jednorázové zřizování  |  |
|-|-|
| Počet objektů | 10 miliónů objektů | 
| Velikost datové sady| TiB ~ 4 |
| Průměrná velikost souboru | ~ 500 KiB (největší soubor: 100 GiB) |
| Nahrát propustnost | 15 objekty za sekundu |
| Namespace stažení propustnost * | 350 objekty za sekundu |
 
* Při vytvoření nového koncového bodu serveru agenta synchronizace souboru Azure není stahovat obsah souboru. První synchronizaci se úplný obor názvů a pak aktivační události na pozadí pro vyvolání ke stažení souborů, buď v plné výši nebo cloudu vrstev, pokud je povolená, cloud vrstvení zásady, které jsou nastaveny na serveru koncového bodu.

| Probíhající synchronizace  |   |
|-|--|
| Počet objektů, které jsou synchronizované| 125,000 objekty (klidové vytížení ~ 1 %) | 
| Velikost datové sady| 50 giB |
| Průměrná velikost souboru | ~ 500 KiB (největší soubor: 100 GiB) |
| Nahrát propustnost | 20 objekty za sekundu |
| Úplné stažení propustnost * | 30 objekty za sekundu |
 
Pokud cloudové vrstvení je povoleno, budete pravděpodobně pozorovat lepší výkon jako jenom některé z stažený data. Synchronizace služby Azure soubor stáhne pouze data uložená v mezipaměti souborů, v případě, že se změní na žádném z koncových bodů. Pro všechny soubory vrstvené nebo nově vytvořené agenta nestáhne datový soubor a místo pouze synchronizuje všechny koncové body serveru oboru názvů. Agent také podporuje částečné stahování vrstvené souborů k nim uživatele. 
 
> [!Note]  
> Výše uvedené čísla nejsou údajem o výkonu, který si všimnete. Skutečným výkonem bude záviset na několika faktory, jak je uvedeno na začátku této části.

Jako obecné informace pro vaše nasazení byste měli mít na paměti několik věcí:
- Objekt propustnost škáluje přibližně v závislosti na počtu skupin synchronizace na serveru. Rozdělení dat do několika skupin synchronizace na serveru dosáhnout lepší propustnosti, která je také omezena serveru a sítě.
- Objekt propustnost je nepřímo úměrná MiB za druhé propustnost. Pro menší soubory bude mít vyšší propustnost z hlediska počet objektů zpracovaných za druhé, ale nižší MiB za druhé propustnost. Naopak pro větší soubory, budete mít menší počet objektů zpracovaných za druhé, ale vyšší MiB za druhé propustnost. MiB za druhé propustnost je omezena cíle škálování Azure Files. 

## <a name="see-also"></a>Další informace najdete v tématech
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Plánování nasazení Azure souboru Sync](storage-sync-files-planning.md)
- [Škálovatelnosti a cílech výkonnosti pro jiné služby úložiště](../common/storage-scalability-targets.md)