---
title: Škálovatelnost a cíle výkonnosti služby Azure Files
description: Seznamte se s cíli škálovatelnosti a výkonu pro soubory Azure, včetně kapacity, míry požadavků a omezení šířky pásma pro příchozí a odchozí připojení.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bdcee6b8ced45dba34309724e5a634cbb60a6d37
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818813"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti služby Azure Files
[Soubory Azure](storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolů systému souborů SMB a NFS. Tento článek popisuje škálovatelnost a výkonnostní cíle pro Azure Files a Synchronizace souborů Azure.

Zde uvedené cíle škálovatelnosti a výkonu jsou špičkové cíle, ale mohou být ovlivněny jinými proměnnými v nasazení. Například propustnost souboru může být omezená také dostupnou šířkou pásma sítě, nejen servery hostujícími sdílené složky Azure. Důrazně doporučujeme testovat vzor používání, abyste zjistili, jestli vaše požadavky vyhovují škálovatelnosti a výkonu souborů Azure. Potvrdili jsme také, že se tato omezení zvyšují v průběhu času. 

## <a name="azure-files-scale-targets"></a>Cíle škálování služby Azure Files
Sdílené složky Azure se nasazují do účtů úložiště, což jsou objekty nejvyšší úrovně, které představují sdílený fond úložiště. Tento fond úložiště se dá použít k nasazení několika sdílených složek. Je proto potřeba zvážit tři kategorie: účty úložiště, sdílené složky Azure a soubory.

### <a name="storage-account-scale-targets"></a>Cíle škálování účtu úložiště
Azure podporuje více typů účtů úložiště pro různé scénáře úložiště, které mohou mít zákazníci, ale existují dva hlavní typy účtů úložiště pro soubory Azure. Typ účtu úložiště, který chcete vytvořit, závisí na tom, jestli chcete vytvořit standardní sdílenou složku nebo sdílenou složku prémiových souborů: 

- **Účty úložiště pro obecné účely verze 2 (GPv2)**: účty úložiště GPv2 umožňují nasadit sdílené složky Azure na hardwaru založeném na standardu a na pevných discích (HDD). Kromě ukládání sdílených složek Azure můžou účty úložiště GPv2 ukládat i další prostředky úložiště, jako jsou kontejnery objektů blob, fronty nebo tabulky. Sdílené složky lze nasadit do optimalizované transakce (výchozí), horké nebo studené úrovně.

- **Účty úložiště** úložiště: účty úložiště úložiště umožňují nasadit sdílené složky Azure na hardware Premium/Solid-State (SSD) na disku (SSD). Účty úložiště souborů se dají použít jenom k ukládání sdílených složek Azure. v účtu úložiště úložiště se nedají nasadit žádné další prostředky úložiště (kontejnery objektů blob, fronty, tabulky atd.).

| Atribut | Účty úložiště GPv2 (Standard) | Účty úložiště úložiště (Premium) |
|-|-|-|
| Počet účtů úložiště podle jednotlivých oblastí a předplatných | 250 | 250 |
| Maximální kapacita účtu úložiště | 5 PiB<sup>1</sup> | 100 TiB (zřízený) |
| Maximální počet sdílených složek | Unlimited | Neomezená celková zřízená velikost všech sdílených složek musí být menší než maximální kapacita účtu úložiště. |
| Maximální počet souběžných požadavků | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Maximální příchozí přenosy | <ul><li>USA/Evropa: 9 536 MiB/s<sup>1</sup></li><li>Další oblasti (LRS/ZRS): 9 536 MiB za sekundu<sup>1</sup></li><li>Další oblasti (GRS): 4 768 GiB/s<sup>1</sup></li></ul> | 4 136 MiB/s |
| Maximální výstup | 47 683 MiB/s<sup>1</sup> | 6 204 MiB/s |
| Maximální počet pravidel virtuální sítě | 200 | 200 |
| Maximální počet pravidel IP adres | 200 | 200 |
| Operace čtení správy | 800 za 5 minut | 800 za 5 minut |
| Operace zápisu pro správu | 10 za sekundu/1200 za hodinu | 10 za sekundu/1200 za hodinu |
| Operace seznamu správy | 100 za 5 minut | 100 za 5 minut |

<sup>1</sup> účty úložiště pro obecné účely verze 2 podporují vyšší limity kapacity a vyšší limity pro příchozí zpracování žádostí. Pokud chcete požádat o zvýšení limitů účtu, obraťte se na [podporu Azure](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Cíle škálování Azure File Share
| Atribut | Standardní sdílené složky<sup>1</sup> | Soubory ke sdílení souborů úrovně Premium |
|-|-|-|
| Minimální velikost sdílené složky | Bez minima | 100 GiB (zřízený) |
| Jednotka pro zvýšení nebo snížení zřízené velikosti | – | 1 GiB |
| Maximální velikost sdílené složky | <ul><li>100 TiB, s povolenou funkcí Velká sdílená složka<sup>2</sup></li><li>5 TiB, výchozí</li></ul> | 100 TiB |
| Maximální počet souborů ve sdílené složce | Bez omezení | Bez omezení |
| Maximální frekvence požadavků (max. IOPS) | <ul><li>10 000 s povolenou funkcí Velká sdílená složka<sup>2</sup></li><li>1 000 nebo 100 požadavků za 100 ms, výchozí</li></ul> | <ul><li>Základní hodnota IOPS: 400 + 1 IOPS na GiB, až 100 000</li><li>Shlukování IOPS: max (4000, 3x IOPS na GiB), až 100 000</li></ul> |
| Maximální příchozí přenos dat pro jednu sdílenou složku | <ul><li>Až 300 MiB/s s povolenou funkcí Velká sdílená složka<sup>2</sup></li><li>Až 60 MiB/s, výchozí</li></ul> | 40 MiB/s + 0,04 * zřízené GiB |
| Maximální počet odchozích dat pro jednu sdílenou složku | <ul><li>Až 300 MiB/s s povolenou funkcí Velká sdílená složka<sup>2</sup></li><li>Až 60 MiB/s, výchozí</li></ul> | 60 MiB/s + 0,06 * zřízené GiB |
| Maximální počet snímků sdílené složky | snímky 200 | snímky 200 |
| Maximální délka názvu objektu (adresářů a souborů) | 2 048 znaků | 2 048 znaků |
| Maximální součást cesty (v cestě \A\B\C\D je každé písmeno součásti) | 255 znaků | 255 znaků |
| Limit pevných odkazů (jenom NFS) | – | 178 |
| Maximální počet vícekanálových kanálů protokolu SMB | Není k dispozici | 4 |
| Maximální počet uložených zásad přístupu na sdílení souborů | 5 | 5 |

<sup>1</sup> limity pro standardní sdílené složky se vztahují na všechny tři úrovně, které jsou dostupné pro standardní sdílené složky: transakce je optimalizovaná, horká a studená.

<sup>2</sup> výchozí u standardních sdílených složek je 5 TIB. podrobné informace o tom, jak zvýšit úroveň standardních sdílených 100 složek, najdete v tématu [povolení a vytváření velkých sdílených složek](./storage-files-how-to-create-large-file-share.md) .

### <a name="file-scale-targets"></a>Cíle škálování souborů
| Atribut | Soubory ve standardních sdílených složkách  | Soubory ve sdílených složkách Premium  |
|-|-|-|
| Maximální velikost souboru | 4 TiB | 4 TiB |
| Maximální počet souběžných požadavků | 1 000 IOPS | Až 8 000<sup>1</sup> |
| Maximální příchozí přenos dat pro soubor | 60 MiB/s | 200 MiB/s (až 1 GiB/s s SMB vícekanálový Preview)<sup>2</sup>|
| Maximální výstup pro soubor | 60 MiB/s | 300 MiB/s (až 1 GiB/s s SMB vícekanálový Preview)<sup>2</sup> |
| Maximální počet souběžných popisovačů | 2 000 popisovačů | 2 000 popisovačů  |

<sup>1 platí pro iOS pro čtení a zápis (obvykle menší velikosti vstupně-výstupních operací menší než nebo rovna 64 KiB). Operace s metadaty, kromě čtení a zápisu, mohou být nižší.</sup> 
 <sup>2 v závislosti na omezeních sítě počítačů, dostupné šířce pásma, vstupně-výstupních operacích, hloubce fronty a dalších faktorech. Podrobnosti najdete v článku o [výkonu vícekanálového protokolu SMB](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Cíle škálování v Synchronizaci souborů Azure
Následující tabulka uvádí hranice testování Microsoftu a také uvádí, které cíle jsou pevné meze:

| Prostředek | Cíl | Omezení pevné velikosti |
|----------|--------------|------------|
| Služby synchronizace úložiště na oblast | 100 služby synchronizace úložiště | Yes |
| Skupiny synchronizace na službu synchronizace úložiště | skupiny synchronizace 200 | Yes |
| Registrované servery na službu synchronizace úložiště | servery 99 | Yes |
| Cloudové koncové body na skupinu synchronizace | 1 koncový bod cloudu | Yes |
| Koncové body serveru na skupinu synchronizace | koncové body serveru 100 | Yes |
| Koncové body serveru na server | 30 koncových bodů serveru | Yes |
| Objekty systému souborů (adresáře a soubory) na skupinu synchronizace | objekty 100 000 000 | No |
| Maximální počet objektů systému souborů (adresářů a souborů) v adresáři | objekty 5 000 000 | Yes |
| Maximální velikost popisovače zabezpečení objektů (adresářů a souborů) | 64 KiB | Yes |
| Velikost souboru | 100 GiB | No |
| Minimální velikost souboru, který se má převrstvený | V9 a novější: na základě velikosti clusteru systému souborů (velikost clusteru systému souborů Double). Pokud je například velikost clusteru systému souborů 4 KiB, minimální velikost souboru bude 8 KiB.<br> V8 a starší verze: 64 KiB  | Yes |

> [!Note]  
> Koncový bod Synchronizace souborů Azure může škálovat až na velikost sdílené složky Azure. Pokud je dosaženo limitu velikosti sdílené složky Azure, nebude synchronizace moct fungovat.

### <a name="azure-file-sync-performance-metrics"></a>Metriky výkonu v Synchronizaci souborů Azure
Vzhledem k tomu, že agent Synchronizace souborů Azure běží na počítači s Windows serverem, který se připojuje ke sdíleným složkám Azure, výkon efektivní synchronizace závisí na několika faktorech v infrastruktuře: Windows Server a základní konfigurace disku, Šířka pásma sítě mezi serverem a úložištěm Azure, velikost souboru, celková velikost datové sady a aktivita v datové sadě. Vzhledem k tomu, že Synchronizace souborů Azure pracuje na úrovni souboru, jsou výkonnostní charakteristiky řešení založeného na Synchronizace souborů Azure lépe měřeny v počtu objektů (souborů a adresářů) zpracovaných za sekundu.

Pro Synchronizace souborů Azure je výkon kritický ve dvou fázích:

1. **Prvotní zřizování**: pro optimalizaci výkonu při počátečním zřizování najdete informace o optimálních podrobnostech o nasazení [pomocí synchronizace souborů Azure](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) .
2. **Průběžná synchronizace**: po počátečním navýšení dat ve sdílených složkách Azure synchronizace souborů Azure udržuje několik koncových bodů v synchronizaci.

Abychom vám pomohli naplánovat nasazení pro každou fázi, níže jsou výsledky zjištěné během interního testování v systému s konfigurací.

| Konfigurace systému | Podrobnosti |
|-|-|
| Procesor | 64 virtuálních jader s 64 MiB L3 cache |
| Memory (Paměť) | 128 GiB |
| Disk | Disky SAS s RAID 10 s mezipamětí zálohovanou pro baterie |
| Síť | 1 GB/s sítě |
| Úloha | Pro obecné účely souborový server|

| Prvotní zřízení v jednom čase  | Podrobnosti |
|-|-|
| Počet objektů | objekty 25 000 000 |
| Velikost datové sady| ~ 4,7 TiB |
| Průměrná velikost souboru | ~ 200 KiB (největší soubor: 100 GiB) |
| Počáteční výčet změn v cloudu | 20 objektů za sekundu  |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Propustnost stahování oboru názvů | 400 objektů za sekundu |

### <a name="initial-one-time-provisioning"></a>Prvotní zřízení v jednom čase

**Počáteční výčet změn v cloudu**: když se vytvoří nová skupina synchronizace, bude první krok, který se spustí, počáteční výčet změn v cloudu. V tomto procesu bude systém vypsat všechny položky ve sdílené složce Azure. Během tohoto procesu nebude žádná aktivita synchronizace, tj. žádné položky nebudou staženy z koncového bodu cloudu do koncového bodu serveru a žádné položky nebudou odeslány z koncového bodu serveru do koncového bodu cloudu. Po dokončení počátečního výčtu změn v cloudu bude aktivita synchronizace pokračovat.
Míra výkonu je 20 objektů za sekundu. Zákazníci si můžou odhadnout čas, který bude trvat, aby dokončili počáteční výčet změn v cloudu tím, že určí počet položek ve sdílené složce cloudu a pomocí následujících vzorců Získá čas ve dnech. 

   **Čas (ve dnech) počátečního výčtu cloudu = (počet objektů v koncovém bodu cloudu)/(20 × 60 × 60 × 24)**

**Počáteční synchronizace dat ze systému Windows Server do sdílené složky Azure**: mnoho nasazení synchronizace souborů Azure začínat prázdnou sdílenou složkou Azure, protože všechna data jsou na Windows serveru. V těchto případech je počáteční výčet změn v cloudu rychlý a většina času stráví synchronizaci změn z Windows serveru až po sdílené složky Azure. 

Zatímco synchronizace nahrává data do sdílené složky Azure, na místním souborovém serveru nedochází k výpadkům a správci můžou [nastavit omezení sítě](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) , aby se omezila šířka pásma využitá pro nahrání dat na pozadí.

Počáteční synchronizace se obvykle omezí na počáteční rychlost nahrávání 20 souborů za sekundu na skupinu synchronizace. Zákazníci můžou odhadnout čas nahrávání všech svých dat do Azure pomocí následujícího vzorce a získat tak dobu ve dnech:  

   **Čas (ve dnech) pro nahrávání souborů do skupiny synchronizace = (počet objektů v koncovém bodu serveru)/(20 × 60 × 60 × 24)**

Rozdělení dat do více koncových bodů serveru a skupin synchronizace může zrychlit toto počáteční nahrání dat, protože nahrávání je možné provést paralelně pro více skupin synchronizace rychlostí 20 položek za sekundu. To znamená, že dvě skupiny synchronizace budou spuštěné v kombinované sazbě 40 položek za sekundu. Celkový čas dokončení by představoval časový odhad pro skupinu synchronizace s nejvíce soubory, které se mají synchronizovat.

**Propustnost stahování oboru názvů** Při přidání nového koncového bodu serveru do existující skupiny synchronizace agent Synchronizace souborů Azure nestáhne žádný obsah souboru z koncového bodu cloudu. Nejprve synchronizuje celý obor názvů a potom aktivuje odvolání na pozadí pro stažení souborů, a to buď v celém rozsahu, nebo v případě, že je povolená vrstva cloudu, do zásady clouding nastavené na koncovém bodu serveru.

| Průběžná synchronizace  | Podrobnosti  |
|-|--|
| Počet synchronizovaných objektů| 125 000 objektů (počet změn: 1%) |
| Velikost datové sady| 50 GiB |
| Průměrná velikost souboru | ~ 500 KiB |
| Propustnost nahrávání | 20 objektů za sekundu na skupinu synchronizace |
| Úplná propustnost ke stažení * | 60 objektů za sekundu |

* Pokud je zapnutá vrstva cloudu, budete pravděpodobně sledovat lepší výkon, protože se stáhnou jenom některá z těchto souborů. Synchronizace souborů Azure stahuje data souborů uložených v mezipaměti pouze při jejich změně v libovolném koncovém bodu. U všech vrstvených nebo nově vytvořených souborů agent nestáhne data souboru a místo toho pouze synchronizuje obor názvů se všemi koncovými body serveru. Agent také podporuje částečné stahování vrstvených souborů, ke kterým uživatel přistupoval. 

> [!Note]  
> Výše uvedená čísla nezpůsobují indikaci výkonu, ke kterému dojde. Skutečný výkon bude záviset na několika faktorech, jak je uvedeno na začátku této části.

Jako obecné vodítko pro vaše nasazení byste měli mít na paměti pár věcí:

- Propustnost objektu se přibližně škáluje v poměru k počtu skupin synchronizace na serveru. Rozdělení dat do více skupin synchronizace na serveru má za důsledek lepší propustnost, což je také omezeno serverem a sítí.
- Propustnost objektu je v opačném poměrně úměrná propustnosti MiB za sekundu. Pro menší soubory dojde k vyšší propustnosti z hlediska počtu zpracovaných objektů za sekundu, ale nižší propustnosti MiB za sekundu. Naopak pro větší soubory získáte méně zpracovaných objektů za sekundu, ale vyšší propustnost MiB za sekundu. Propustnost MiB za sekundu je omezená na cíle škálování souborů Azure.

## <a name="see-also"></a>Viz také
- [Plánování nasazení Azure Files](storage-files-planning.md)
- [Plánování nasazení Synchronizace souborů Azure](../file-sync/file-sync-planning.md)