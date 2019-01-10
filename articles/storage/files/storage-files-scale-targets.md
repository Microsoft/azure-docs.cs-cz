---
title: Azure soubory škálovatelnost a výkonnostní cíle | Dokumentace Microsoftu
description: Další informace o škálovatelnost a výkonnostní cíle pro soubory Azure, včetně kapacity, frekvence požadavků a omezení šířky příchozího a odchozího pásma.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 7/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 05e5c0a37d2de78393048728b73d9bcf6e56c491
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159162"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure soubory škálovatelnost a výkonnostní cíle
[Služba soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes standardní protokol SMB. Tento článek popisuje škálovatelnost a výkonnostní cíle pro soubory Azure a Azure File Sync.

Škálovatelnost a výkonnostní cíle, které jsou zde uvedeny jsou vyšší kategorie cíle, ale mohou být ovlivněny jiné proměnné ve vašem nasazení. Například propustnost pro soubor může být také omezen ve vaši dostupnou šířku pásma sítě, nikoli pouze servery, které hostují služby soubory Azure. Důrazně doporučujeme testování vaší vzor používání a zjistit, zda škálovatelnost a výkon Azure Files splňují vaše požadavky. Můžeme také usilujeme o to zvyšujícími se tato omezení v čase. Neváhejte sdělte nám svůj názor, buď v komentářích pod nebo na [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files), o které limity byste chtěli vidět nám zvýšit.

## <a name="azure-storage-account-scale-targets"></a>Cíle škálování účtu Azure storage
Nadřazený prostředek pro sdílené složky Azure je účet úložiště Azure. Účet úložiště představuje fond úložiště v Azure, které je možné k ukládání dat pomocí několika služeb úložiště, včetně souborů Azure. Jiné služby, které ukládají data v účtech úložiště, jsou Azure Blob storage, Azure Queue storage a Azure Table storage. Následující cíle, které se vztahují všechny služby storage, ukládání dat v účtu úložiště:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Využití úložiště účtu z jiných služeb úložiště ovlivňuje sdílených složek Azure v účtu úložiště. Například pokud dosáhnete maximální úložnou kapacitu účtu úložiště objektů Blob v Azure, nebudete moct vytvořit nové soubory ve sdílené složky Azure, i v případě, že sdílené složky Azure je nižší než maximální sdílené složky size.

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle
Pomocí služby Azure File Sync jsme jste se pokusili co nejvíc návrhu pro neomezené využití, ale to není vždy možné. Následující tabulka označuje hranice naše testování a cíle, které jsou ve skutečnosti pevných limitů:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu služby Azure File Sync
Agenta Azure File Sync běží na počítače s Windows serverem, který se připojuje k sdílené složky Azure, výkonu efektivní synchronizace závisí na řadě faktorů ve vaší infrastruktuře: Windows Server a základní konfiguraci disku, šířky pásma sítě mezi serverem a Azure storage, soubor velikost, velikost celkového datové sady a aktivity na datové sadě. Protože Azure File Sync funguje na úrovni souboru, výkonové charakteristiky řešení založené na Azure File Sync se lépe měří v počet objektů (soubory a adresáře) zpracovaných za sekundu. 
 
Výkon pro Azure File Sync, je důležité ve dvou fázích:
1. **Počáteční jednorázové zřizování**: Optimalizace výkonu v počátečním zřizování, najdete v tématu [připojování pomocí služby Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) podrobnosti optimální nasazení.
2. **Probíhající synchronizace**: Po dat je zpočátku nasadí do sdílené složky Azure, Azure File Sync udržuje několik koncových bodů synchronizované.

Při plánování nasazení pro každou z fází, níže jsou výsledky pozorovány při interním testování v systému, konfigurace

| Konfigurace systému |  |
|-|-|
| Procesor | 64 virtuálních jader s 64 MiB L3 |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS pomocí diskového pole RAID 10 s baterie podporou mezipaměti |
| Síť | 1 GB/s síť |
| Úloha | Souborový Server pro obecné účely|

| Počáteční jednorázové zřizování  |  |
|-|-|
| Počet objektů | 10 milionů objektů | 
| Velikost datové sady| ~ 4 TB |
| Průměrná velikost souboru | ~ 500 KiB (největší soubor: 100 GB) |
| Nahrát propustnost | 20 objekty za sekundu |
| Namespace stahování propustnost * | 400 objekty za sekundu |
 
* Když se vytvoří nový koncový bod serveru, agenta Azure File Sync nebude stahovat žádný obsah souboru. Nejprve synchronizuje úplný obor názvů a pak aktivační události na pozadí spojené s vracením ke stažení souborů, buď v plné výši nebo vrstvení cloudu, pokud je povoleno, nastavte na koncovém bodu serveru zásad vrstvení cloudu.

| Probíhající synchronizace  |   |
|-|--|
| Počet objektů, které jsou synchronizovány| 125,000 objekty (klidové vytížení ~ 1 %) | 
| Velikost datové sady| 50 GB |
| Průměrná velikost souboru | ~ 500 KiB |
| Nahrát propustnost | objekty 30 za sekundu |
| Úplné stažení propustnost * | 60 objekty za sekundu |
 
* Pokud cloudu ovládání datových vrstev je povolená, budete pravděpodobně sledovat lepší výkon jako pouze některá data se stáhne soubor. Azure File Sync stáhne jenom data uložená v mezipaměti souborů, když se změní na žádném z koncových bodů. Vrstvené nebo nově vytvořené soubory agent nebude stahovat data souborů a místo toho synchronizuje pouze obor názvů pro všechny koncové body serveru. Agent také podporuje částečné stažení vrstvené soubory jsou přístupné uživatelem. 
 
> [!Note]  
> Výše uvedených čísel nejsou údaj o výkon, který bude probíhat. Skutečný výkon bude záviset na několika faktorech, jak je uvedeno na začátku této části.

Jako obecné vodítko pro vaše nasazení byste měli mít na paměti několik věcí:
- Objekt propustnost přibližně škáluje poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru poskytuje vyšší propustnost, což je také omezena serveru a sítě.
- Objekt propustnost je nepřímo úměrná MiB za druhé propustnost. Pro menší soubory se budou mít vyšší výkon z hlediska počtu objektů zpracovaných za druhé, ale nižší MiB za druhé propustnost. Naopak pro větší soubory, zobrazí se méně objektů zpracovaných za druhé, ale vyšší MiB za druhé propustnost. MiB za druhé propustnost je omezená cíle škálování Azure Files. 

## <a name="see-also"></a>Další informace najdete v tématech
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)
- [Plánování nasazení služby Azure File Sync](storage-sync-files-planning.md)
- [Škálovatelnost a výkonnostní cíle pro ostatní služby úložiště](../common/storage-scalability-targets.md)
