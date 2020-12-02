---
title: Typy úložiště Azure pro úlohu SAP
description: Plánování typů úložiště Azure pro úlohy SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6982b782fdd6b5b269c1562c54be3478c58bbce9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500993"
---
# <a name="azure-storage-types-for-sap-workload"></a>Typy služby Azure Storage pro úlohy SAP
Azure má spoustu typů úložiště, které se v různých možnostech, propustnosti, latenci a cenách liší. Některé typy úložiště nejsou ani omezené možnosti použitelné pro scénáře SAP. Vzhledem k tomu, že některé typy úložiště Azure jsou vhodné nebo optimalizované pro konkrétní scénáře úloh SAP. Zejména u SAP HANA některé typy úložiště Azure získali certifikaci pro použití s SAP HANA. V tomto dokumentu procházíme mezi různými typy úložišť a popisujete jejich schopnost a použitelnost pomocí úloh SAP a komponent SAP.

Přeoznačení jednotek používaných v rámci tohoto článku. Dodavatelé veřejného cloudu přesunuli, aby používali GiB ([gibibajt](https://en.wikipedia.org/wiki/Gibibyte)) nebo TIB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) jako jednotky velikostí místo gigabajtů a terabajtů. Proto všechna dokumentace a cenám Azure tyto jednotky používají.  V celém dokumentu odkazujeme výhradně na tyto jednotky formátu MiB, GiB a TiB jednotky. Možná budete muset naplánovat na MB, GB a TB. Proto si pamatujte na některé malé rozdíly ve výpočtech, pokud potřebujete velikost pro propustnost 400 MiB/s, nikoli propustnost 250 MiB/s.

## <a name="microsoft-azure-storage-resiliency"></a>Odolnost Microsoft Azure Storage

Microsoft Azure úložiště HDD úrovně Standard, SSD úrovně Standard, Azure Premium Storage a Ultra disk udržuje základní virtuální pevný disk (s operačním systémem) a disky s připojenými daty virtuálních počítačů ve třech kopiích na třech různých uzlech úložiště. Převzetí služeb při selhání jinou replikou a osazení nové repliky v případě selhání uzlu úložiště je transparentní. V důsledku tohoto redundance **není nutné na** více discích Azure použít žádný druh záložní vrstvy úložiště. Tento fakt se nazývá Local redundantní úložiště (LRS). LRS je výchozí pro tyto typy úložiště v Azure. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) poskytuje dostatečnou redundanci, aby bylo možné dosáhnout stejného SLA jako jiné nativní úložiště Azure.

Existuje několik dalších metod redundance, které jsou popsány v článku [Azure Storage replikaci](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) , která platí pro některé z různých typů úložiště, které Azure nabízí. 

Pamatujte na to, že různé typy úložiště Azure mají vliv na SLA dostupnosti jediného virtuálního počítače, který je vydaný ve [smlouvě SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="azure-managed-disks"></a>Azure Managed disks

Managed disks je typ prostředku v Azure Resource Manager, který se dá použít místo VHD, které jsou uložené v účtech Azure Storage. Managed Disks automaticky zarovnává se [skupinou dostupnosti] [virtuální počítače-spravovat-dostupnost] virtuálního počítače, ke kterému jsou připojené, a proto Zvyšte dostupnost vašeho virtuálního počítače a služeb, které běží na virtuálním počítači. Další informace najdete v [článku Přehled](../../managed-disks-overview.md).

V souvislosti s odolností tento příklad ukazuje výhody spravovaných disků:

- Nasazujete dva virtuální počítače DBMS pro váš systém SAP v sadě dostupnosti Azure. 
- Když Azure nasadí virtuální počítače, disk s bitovou kopií operačního systému se umístí do jiného clusteru úložiště. Tím předejdete tomu, že oba virtuální počítače mají dopad na problém s jedním clusterem úložiště Azure.
- Když vytváříte nové spravované disky, které těmto virtuálním počítačům přiřadíte pro ukládání souborů dat a protokolů vaší databáze, tyto nové disky pro tyto dva virtuální počítače se nasadí taky v samostatných clusterech úložiště, takže to, že žádný z disků prvního virtuálního počítače nesdílí clustery úložiště s disky druhého virtuálního počítače.

Nasazování bez spravovaných disků v účtech úložiště definovaných zákazníky, přidělení disku je libovolné a nemá žádné povědomí o tom, že se virtuální počítače nasazují v rámci AvSet pro účely odolnosti.

> [!NOTE]
> Z tohoto důvodu a několika dalších vylepšení, která jsou exkluzivně dostupná prostřednictvím spravovaných disků, vyžadujeme, aby nová nasazení virtuálních počítačů, které používají blokové úložiště Azure pro své disky (všechna úložiště Azure kromě Azure NetApp Files), používala Azure Managed disks pro základní disky VHD/OS, datové disky, které obsahují soubory databáze SAP. Nezávisle na tom, jestli nasazujete virtuální počítače prostřednictvím skupiny dostupnosti, napříč Zóny dostupnosti nebo nezávisle na sadách a zónách. Disky, které se používají pro účely ukládání záloh, nemusí být nutně nutné pro spravované disky.

> [!NOTE]
> Azure Managed disks poskytuje jenom místní redundanci (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Scénáře úložiště s úlohami SAP
Trvalé úložiště je potřeba v úlohách SAP v různých součástech zásobníku, který nasazujete v Azure. Tento seznam scénářů je minimálně podobný:

- Zajistěte si základní virtuální pevný disk virtuálního počítače, který obsahuje operační systém a další software, který na tento disk nainstalujete. Tento disk/VHD je kořenem virtuálního počítače. Jakékoli změny, které v něm provedete, musí být trvalé. To znamená, že při příštím zastavení a restartování virtuálního počítače se všechny změny provedly ještě před tím, než budou existovat. Zejména v případech, kdy se virtuální počítač nasazuje Azure na jiného hostitele, než byl původně spuštěný.
- Trvalé datové disky. Tyto disky jsou virtuální pevné disky, které připojíte k ukládání dat aplikací v. Tato data aplikace by mohla být data a protokolovat nebo opakovat soubory databáze, záložních souborů nebo instalací softwaru. Znamená, že každý disk je mimo váš základní virtuální pevný disk, který obsahuje operační systém.
- Sdílené složky nebo sdílené disky, které obsahují váš globální adresář pro přenos pro NetWeaver nebo S/4HANA. Obsah těchto sdílených složek je využíván softwarem spuštěným ve více virtuálních počítačích nebo při vytváření scénářů clusteru s podporou převzetí služeb při selhání s vysokou dostupností.
- Adresář/sapmnt nebo běžné sdílené složky pro procesy EDI nebo podobné. Obsah těchto sdílených složek je využíván softwarem spuštěným ve více virtuálních počítačích nebo při vytváření scénářů clusteru s podporou převzetí služeb při selhání s vysokou dostupností.

V následujících částech se v několika dalších typech úložiště Azure a jejich použitelnost pro úlohy SAP prodiskutuje, které se vztahují na výše uvedené čtyři scénáře. V článku o tom, [Jaké typy disků jsou k dispozici v Azure](../../disks-types.md), je popsána obecná kategorizace způsobu použití různých typů úložiště Azure. Doporučení pro používání různých typů úložiště Azure pro úlohy SAP se neúčtují podstatným rozdílem.

Pro omezení podpory typů úložiště Azure pro NetWeaver/aplikační vrstvu SAP pro 4HANA si přečtěte [poznámku o podpoře SAP 2015553](https://launchpad.support.sap.com/#/notes/2015553) pro SAP HANA certifikovaný a podporované typy úložiště Azure Přečtěte si článek [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).

Oddíly, které popisují různé typy úložiště Azure, vám poskytnou další informace o omezeních a možnostech pomocí úložiště podporovaného SAP. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Doporučení pro úložiště pro scénáře úložiště SAP
Než přejdete k podrobnostem, prezentujeme Shrnutí a doporučení, které už na začátku dokumentu jsou. V této části dokumentu jsou uvedené podrobnosti o konkrétních typech služby Azure Storage. Shrnutí doporučení úložiště pro scénáře úložiště SAP v tabulce vypadá takto:

| Scénář použití | HDD úrovně Standard | SSD úrovně Standard | Premium Storage | Disky Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Disk OS | nevhodné |  vhodné (nepatří do výrobního typu) | doporučil | není možné | není možné |
| Adresář globálního přenosu | nepodporováno | nepodporováno | doporučil | doporučil | doporučil |
| /sapmnt | nevhodné | vhodné (nepatří do výrobního typu) | doporučil | doporučil | doporučil |
| Řady virtuálních počítačů s SAP HANA M/Mv2 pro DBMS dat | nepodporováno | nepodporováno | doporučil | doporučil | Doporučené<sup>2</sup> |
| DBMS – svazek protokolu SAP HANA rodin virtuálních počítačů M/Mv2 | nepodporováno | nepodporováno | Doporučené<sup>1</sup> | doporučil | Doporučené<sup>2</sup> | 
| DBMS data Volume SAP HANA Esv3/Edsv4 rodiny virtuálních počítačů | nepodporováno | nepodporováno | doporučil | doporučil | Doporučené<sup>2</sup> |
| DBMS protokol Volume SAP HANA Esv3/Edsv4 rodiny virtuálních počítačů | nepodporováno | nepodporováno | nepodporováno | doporučil | Doporučené<sup>2</sup> | 
| DBMS datový svazek, který není HANA | nepodporováno | vhodné (nepatří do výrobního typu) | doporučil | doporučil | nepodporováno |
| DBMS protokolovat řady virtuálních počítačů, které nejsou HANA/HANA M/Mv2 | nepodporováno | vhodné (nepatří do výrobního typu) | Doporučené<sup>1</sup> | doporučil | nepodporováno |
| DBMS protokoluje řady virtuálních počítačů, které nejsou M/HANA/Mv2. | nepodporováno | vhodné (nepatří do výrobního typu) | vhodné pro až středně velké zatížení | doporučil | nepodporováno |


<sup>1</sup> s využitím služby [Azure akcelerátor zápisu](../../how-to-enable-write-accelerator.md) pro řady virtuálních počítačů M/Mv2 pro svazky protokolu/opakování protokolů <sup>2</sup> pomocí ANF vyžaduje/Hana/data a také/Hana/log pro ANF. 

Vlastnosti, které můžete očekávat od různých typů úložišť, jako je:

| Scénář použití | HDD úrovně Standard | SSD úrovně Standard | Premium Storage | Disky Ultra | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Smlouva SLA pro propustnost/IOPS | ne | ne | ano | ano | ano |
| Čtení latencí | high | střední až vysoká | slab | dílčí milisekunda | dílčí milisekunda |
| Zápisy latence | high | střední až vysoká  | nízká (dílčí – milisekunda<sup>1</sup>) | dílčí milisekunda | dílčí milisekunda |
| Podporuje HANA | ne | ne | Ano<sup>1</sup> | ano | ano |
| Možné snímky disku | ano | ano | ano | ne | ano |
| Přidělení disků v různých clusterech úložiště při používání skupin dostupnosti | prostřednictvím spravovaných disků | prostřednictvím spravovaných disků | prostřednictvím spravovaných disků | typ disku není podporovaný virtuálními počítači nasazenými prostřednictvím skupin dostupnosti. | žádné<sup>3</sup> |
| Zarovnáno s Zóny dostupnosti | ano | ano | ano | ano | vyžaduje zapojení Microsoftu |
| Oblast redundance | nepoužívá se pro spravované disky | nepoužívá se pro spravované disky | nepoužívá se pro spravované disky | ne | ne |
| Geografická redundance | nepoužívá se pro spravované disky | nepoužívá se pro spravované disky | ne | ne | ne |


<sup>1</sup> s využitím [Azure akcelerátor zápisu](../../how-to-enable-write-accelerator.md) pro řady virtuálních počítačů M/Mv2 pro svazky protokolů/opakování

<sup>2</sup> náklady závisí na ZŘÍZENých vstupně-výstupních operacích a propustnosti.

<sup>3</sup> vytvoření různých fondů kapacit ANF nezaručuje nasazení fondů kapacity do různých jednotek úložiště.


> [!IMPORTANT]
> Aby bylo možné dosáhnout méně než 1 milisekundy na latenci vstupu a výstupu pomocí Azure NetApp Files (ANF), budete muset spolupracovat s Microsoftem, aby bylo možné uspořádat správné umístění mezi vašimi virtuálními počítači a sdílenými složkami NFS na základě ANF. Zatím není k dispozici žádný mechanismus, který poskytuje automatickou blízkost mezi nasazeným virtuálním počítačem a svazky NFS hostovanými na ANF. Vzhledem k různým nastavením různých oblastí Azure může být přidaná latence sítě nabízena přes 1 milisekundovou latenci, pokud virtuální počítač a sdílená složka NFS nejsou přiděleny v blízkosti.


> [!IMPORTANT]
> Žádné z aktuálně nabízené služby Azure Block Storage na základě úložiště není k dispozici ani Azure NetApp Files nabízet jakékoli geografické nebo zeměpisné redundance. V důsledku toho je potřeba zajistit, aby se vaše architektury pro obnovení s vysokou dostupností a zotavení po havárii nespoléhaly na žádný typ replikace nativního úložiště Azure pro tyto spravované disky, soubory NFS nebo sdílené složky protokolu SMB.


## <a name="azure-premium-storage"></a>Azure Premium Storage
Úložiště Azure Premium SSD bylo zavedeno s cílem poskytnout:

* Nízká latence vstupu/výstupu
* SLA pro IOPS a propustnost
* Menší variabilita v/v latence

Tento typ úložiště cílí na úlohy DBMS, provoz úložiště, který vyžaduje latenci s nízkou dobou v milisekundách, a v případě Azure Premium Storage se SLA na zatížení za vstupně-výstupní operace a náklady na propustnost, a to v případě, že se v případě Azure Premium Storage nejedná o skutečný objem dat, který je na těchto discích uložený. Můžete také vytvořit disky na Premium Storage, které nejsou přímo mapovány na kategorie velikosti uvedené v článku [SSD úrovně Premium](../../disks-types.md#premium-ssd). K závěrům z tohoto článku patří:

- Úložiště je uspořádáno v oblastech. Například disk v rozsahu od 513 GiB do 1024 kapacity GiB sdílí stejné možnosti a stejné měsíční náklady
- Počet IOPS na GiB nesleduje lineární napříč kategoriemi velikostí. Menší disky nižší než 32 GiB mají za GiB vyšší sazby za IOPS. U disků přesahujících 32 GiB až 1024 GiB je sazba IOPS za GiB mezi 4-5 IOPS za GiB. V případě větších disků až do 32 767 GiB je sazba IOPS za GiB nižší než 1.
- Propustnost vstupně-výstupních operací pro toto úložiště není lineární pro velikost kategorie disku. Pro menší disky, jako je kategorie mezi 65 GiB a 128 GiB kapacita, je propustnost kolem 780KB/GiB. Vzhledem k tomu, že pro extrémní velké disky, jako je 32 767 GiB disk, je propustnost kolem 28KB/GiB
- VSTUPNĚ-výstupní operace a SLA propustnosti nelze změnit, aniž by došlo ke změně kapacity disku.


Matrice schopností pro úlohu SAP vypadá takto:

| Schopnost| Komentář| Poznámky a odkazy | 
| --- | --- | --- | 
| Základní virtuální pevný disk operačního systému | vhodnou | Všechny systémy |
| Datový disk | vhodnou | Všechny systémy – [speciálně pro SAP HANA](../../how-to-enable-write-accelerator.md) |
| Adresář globálního přenosu SAP | ANO | [Podporováno](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | vhodnou | Všechny systémy |
| Úložiště zálohování | vhodnou | pro krátkodobé ukládání záloh |
| Sdílené složky/sdílený disk | není dostupná | Vyžaduje soubory Azure Premium nebo třetí stranu. |
| Odolnost | LRS | Pro disky nejsou k dispozici žádné GRS ani ZRS. |
| Latence | nízké až střední | - |
| SLA PRO IOPS | ANO | - |
| IOPS lineární na kapacitu | poloviční lineární v závorkách  | [Ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximální počet IOPS na disk | 20 000 [závislý na velikosti disku](https://azure.microsoft.com/pricing/details/managed-disks/) | Zvážit také [omezení počtu virtuálních počítačů](../../sizes.md) |
| Smlouva SLA pro propustnost | ANO | - |
| Propustnost lineární pro kapacitu | poloviční lineární v závorkách | [Ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certified HANA | ANO | [speciálně pro SAP HANA](../../how-to-enable-write-accelerator.md) |
| Možné snímky disku | ANO | - |
| Možné snímky Azure Backup VM | ANO | s výjimkou [akcelerátor zápisu](../../how-to-enable-write-accelerator.md) disků uložených v mezipaměti  |
| Náklady | ÚROVNĚ | - |

Azure Premium Storage nesplňuje SAP HANA klíčových ukazatelů výkonu úložiště s běžnými typy ukládání do mezipaměti, které nabízí Azure Premium Storage. Aby bylo možné splnit klíčové ukazatele výkonu latence úložiště pro zápisy do protokolu SAP HANA, je nutné použít službu Azure Akcelerátor zápisu Caching, jak je popsáno v článku [povolení akcelerátor zápisu](../../how-to-enable-write-accelerator.md). Azure Akcelerátor zápisu přináší všechny ostatní systémy DBMS pro jejich zápisy do protokolu transakcí a zápisy do protokolu znovu. Proto se doporučuje používat v rámci všech nasazení SAP DBMS. Pro SAP HANA je povinná použití Azure Akcelerátor zápisu ve spojení se službou Azure Premium Storage.



**Shrnutí:** Azure Premium Storage je jedním z typů úložiště Azure doporučených pro úlohy SAP. Toto doporučení se vztahuje i na neprodukční systémy i na produkční systémy. Služba Azure Premium Storage je vhodná pro zpracování databázových úloh. Využití služby Azure Akcelerátor zápisu v podstatě vylepšuje latenci zápisu na diskech Azure Premium. Pro systémy DBMS s vysokými IOPS a propustností ale potřebujete buď zajistit kapacitu úložiště, nebo potřebujete použít funkce, jako jsou prostory úložiště Windows nebo Správci logických svazků v systému Linux, a vytvořit tak sady prokládaných dat, která vám poskytne požadovanou kapacitu na jedné straně, ale také potřebný vstupně-výstupní operace nebo propustnost při dosažení nejlepšího nákladového efektivity.


### <a name="azure-burst-functionality-for-premium-storage"></a>Funkce Azure Burst pro Premium Storage
Pro disky Azure Premium Storage menší nebo rovny 512 GiB v kapacitě se nabízí funkce shlukování. Přesný způsob, jak funguje shlukování disku, je popsaný v článku o rozložení [disku na disk](../../disk-bursting.md). Po přečtení článku rozumíte konceptu časově rozlišených vstupně-výstupních operací a propustnosti v časech, kdy je zatížení v/v pod nominálními IOPS a propustností disků (podrobnosti o nominální propustnosti najdete v tématu [ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/)). Chystáte se rozlišit rozdíl mezi vstupně-výstupními operacemi a propustností mezi aktuálním využitím a jmenovitými hodnotami disku. Počet shluků je omezen na maximálně 30 minut.

V ideálních případech, kde je možné naplánovat tuto funkci shlukování, se pravděpodobně jedná o svazky nebo disky, které obsahují datové soubory pro různé systémy DBMS. U vstupně-výstupních úloh, které jsou na těchto svazcích očekávány, se očekává, že budou vypadat jako v případě malých až středních systémů.

- Nízká až střední zátěž pro čtení, protože data v ideálním případě jsou ukládána do mezipaměti v paměti nebo jako v případě HANA by měla být celá v paměti.
- Shluky zápisu aktivované kontrolními body databáze nebo úložných bodů, které se vydávají pravidelně
- Úlohy zálohování, které se čtou do souvislého streamu v případech, kdy se zálohy neprovádějí pomocí snímků úložiště
- Pro SAP HANA načtení dat do paměti po restartování instance

Obzvláště na menších systémech DBMS, kde vaše úloha zpracovává jenom několik stovek transakcí za sekundu, může tato funkce zvýšit smysl i pro disky nebo svazky, které uchovávají transakce nebo protokol opětovného přihlášení. Očekávaná úloha na disku nebo svazcích vypadá takto:

- Pravidelné zápisy na disk, které jsou závislé na úloze, a povaha úlohy, protože každé potvrzení vydané aplikací je nejspíš aktivovat vstupně-výstupní operace.
- Vyšší zatížení v propustnosti pro případy provozních úloh, jako je vytváření nebo opětovné sestavení indexů
- Čtení shluků při provádění protokolu transakcí nebo zálohování protokolu znovu


## <a name="azure-ultra-disk"></a>Azure Ultra disk
Disky Azure Ultra zajišťují vysokou propustnost, vysoké IOPS a představují konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure IaaS. Mezi další výhody disků Ultra patří možnost dynamicky měnit IOPS a propustnost disku společně s vašimi úlohami, aniž by bylo nutné restartovat virtuální počítače. Disky Ultra jsou vhodné pro úlohy náročné na data, například na úlohy SAP DBMS. Disky Ultra lze použít pouze jako datové disky a nelze je použít jako základní disk VHD, který ukládá operační systém. Doporučujeme použití Azure Premium Storage jako disku VHD na bázi.

Při vytváření Ultra disk máte tři dimenze, které můžete definovat:

- Kapacita disku. Rozsahy jsou 4 GiB až 65 536 GiB.
- Zřízené IOPS pro disk. Na kapacitu disku se vztahují různé maximální hodnoty. Další podrobnosti najdete v článku o [Ultra discích](../../disks-types.md#ultra-disk) .
- Zajištěná šířka pásma úložiště. Liší se maximální šířka pásma závislá na kapacitě disku. Další podrobnosti najdete v článku o [Ultra discích](../../disks-types.md#ultra-disk) .

Náklady na jeden disk se určují podle tří dimenzí, které můžete pro konkrétní disky definovat samostatně. 


Matrice schopností pro úlohu SAP vypadá takto:

| Schopnost| Komentář| Poznámky a odkazy | 
| --- | --- | --- | 
| Základní virtuální pevný disk operačního systému | nefunguje | - |
| Datový disk | vhodnou | Všechny systémy  |
| Adresář globálního přenosu SAP | ANO | [Podporováno](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | vhodnou | Všechny systémy |
| Úložiště zálohování | vhodnou | pro krátkodobé ukládání záloh |
| Sdílené složky/sdílený disk | není dostupná | Vyžaduje třetí stranu |
| Odolnost | LRS | Pro disky nejsou k dispozici žádné GRS ani ZRS. |
| Latence | velmi nízké | - |
| SLA PRO IOPS | ANO | - |
| IOPS lineární na kapacitu | poloviční lineární v závorkách  | [Ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximální počet IOPS na disk | 1 200 až 160 000 | závislá na kapacitě disku |
| Smlouva SLA pro propustnost | ANO | - |
| Propustnost lineární pro kapacitu | poloviční lineární v závorkách | [Ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Certified HANA | ANO | - |
| Možné snímky disku | NO | - |
| Možné snímky Azure Backup VM | NO | - |
| Náklady | Vyšší než Premium Storage | - |



**Shrnutí:** Disky Azure Ultra jsou vhodným úložištěm s nízkou latencí pro všechny druhy úloh SAP. V tomto případě se Ultra disk dá použít jenom v kombinaci s virtuálními počítači nasazenými prostřednictvím Zóny dostupnosti (nasazení na základě prostředí). Ultra disk v tuto chvíli nepodporují snímky úložiště. V opačném případě se u všech ostatních úložišť nedá použít Ultra disk pro základní disk VHD. Ultra disk je ideální pro případy, kdy se v/v zatížení pohybuje hodně a chcete přizpůsobit nasazenou propustnost úložiště nebo IOPS do vzorů úloh úložiště, místo aby se změnila velikost pro maximální využití šířky pásma a IOPS.


## <a name="azure-netapp-files-anf"></a>Soubory Azure NetApp (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) je výsledkem spolupráce mezi společností Microsoft a NetApp s cílem zajistit vysokou výkon nativních souborů NFS a sdílených složek SMB Azure. Důrazem je poskytnout úložiště s vysokou šířkou pásma a nízkou latencí, které umožňuje scénáře nasazení systému DBMS a v průběhu času umožňuje typickou provozní funkčnost úložiště NetApp i přes Azure. Sdílené složky NFS/SMB se nabízejí ve třech různých úrovních služeb, které rozlišují propustnost úložiště a cenu. Úrovně služeb jsou popsány v článku [úrovně služeb pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Pro různé typy úloh SAP se důrazně doporučuje následující úrovně služeb:

- Zpracování úloh SAP DBMS: výkon, ideální pro Ultra
- SAPMNT sdílená složka: výkon, ideální pro Ultra
- Adresář globálního přenosu: výkon, ideální pro Ultra

> [!NOTE]
> Minimální velikost zřizování je 4 TiB jednotka, která se nazývá fond kapacit. Pak vytvoříte svazky z tohoto fondu kapacity. Vzhledem k tomu, že nejmenší objem, který můžete sestavit, je 100 GiB. Fond kapacit můžete rozšířit v TiB krocích. Ceny najdete v článku [Azure NetApp Files ceny](https://azure.microsoft.com/pricing/details/netapp/) .

Úložiště ANF se v současné době podporuje u několika scénářů pro úlohy SAP:

- Poskytování sdílených složek SMB nebo NFS pro globální transportní adresář SAP
- Sdílená složka sapmnt ve scénářích s vysokou dostupností, jak je popsáno v:
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP](./high-availability-guide-windows-netapp-files-smb.md)
    - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md)
    - [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA nasazení pomocí systému souborů NFS verze 4.1 sdílené složky pro svazky/Hana/Shared a/Hana/log a svazky se systémem souborů NFS v 4.1 nebo NFS v3 pro svazky popsané v článku [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md)

> [!NOTE]
> Pro soubory NFS ani sdílené složky SMB založené na Azure NetApp Files nepodporují žádné další úlohy DBMS. Pokud se změní, budou poskytnuty aktualizace a změny.

Stejně jako v případě Azure Premium Storage může být pevná nebo lineární velikost propustnosti na GB problém, když se vyžaduje, aby v propustnosti dodržovala určitá minimální čísla. Podobně jako v tomto případě SAP HANA. V ANF se tento problém může vyslovit rychleji než u Azure Premium disk. V případě disku Azure Premium můžete využít několik menších disků s poměrně vysokou propustností na GiB a roztáhnout do nich, aby byly nákladově efektivní a měly vyšší propustnost při nižší kapacitě. Tento druh rozkládání nefunguje pro soubory NFS nebo sdílené složky SMB hostované v ANF. Toto omezení vedlo k nasazení nadměrnáho typu, například:

- Chcete-li například dosáhnout propustnosti 250 MiB/s na svazku systému souborů NFS hostovaného na ANF, je třeba nasadit 1,95 TiB kapacity úrovně služeb Ultra. 
- Abyste dosáhli 400 MiB/s, museli byste nasadit kapacitu 3,125 TiB. Ale možná budete potřebovat zajistit kapacitu, abyste dosáhli propustnosti, kterou na svazku požadujete. Toto nadměrné poskytování kapacity má vliv na ceny menších instancí HANA. 
- V prostoru pro ANF služby SAP/sapmnt v prostoru, kde se nachází systém souborů NFS, se obvykle chystáte s minimální kapacitou 100 GiB až 150 GiB, kterou vynutila Azure NetApp Files. Prostředí pro zákazníky však ukázalo, že související propustnost 12,8 MiB/s (s použitím Ultra Level Service) nemusí být dostatečná a může mít negativní dopad na stabilitu systému SAP. V takových případech by se zákazníci mohli vyhnout problémům tím, že zvyšují objem/sapmnt svazku, takže se k tomuto svazku dodávají další propustnosti.

Matrice schopností pro úlohu SAP vypadá takto:

| Schopnost| Komentář| Poznámky a odkazy | 
| --- | --- | --- | 
| Základní virtuální pevný disk operačního systému | nefunguje | - |
| Datový disk | vhodnou | Pouze SAP HANA  |
| Adresář globálního přenosu SAP | ANO | SMB i NFS |
| SAP sapmnt | vhodnou | Všechny systémy SMB (jenom Windows) nebo NFS (jenom Linux) |
| Úložiště zálohování | vhodnou | - |
| Sdílené složky/sdílený disk | ANO | SMB 3,0, NFS v3 a NFS verze 4.1 |
| Odolnost | LRS | Pro disky nejsou k dispozici žádné GRS ani ZRS. |
| Latence | velmi nízké | - |
| SLA PRO IOPS | ANO | - |
| IOPS lineární na kapacitu | výhradně lineární  | Závislý na [úrovni služby](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Smlouva SLA pro propustnost | ANO | - |
| Propustnost lineární pro kapacitu | poloviční lineární v závorkách | Závislý na [úrovni služby](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| Certified HANA | ANO | - |
| Možné snímky disku | ANO | - |
| Možné snímky Azure Backup VM | NO | - |
| Náklady | Vyšší než Premium Storage | - |


Další integrovaná funkce úložiště ANF:

- Schopnost provádět snímky svazku
- Klonování svazků ANF ze snímků
- Obnovení svazků ze snímků (modul snap-vrácení)

**Shrnutí**: Azure NetApp Files je úložiště s nízkou latencí Hana, které umožňuje nasadit svazky nebo sdílené složky systému souborů NFS a SMB. Úložiště obsahuje tři různé úrovně služeb, které poskytují různé propustnosti a vstupně-výstupní operace lineárním způsobem na GiBou kapacitu svazku. Úložiště ANF umožňuje nasazení SAP HANAch scénářů škálování na více instancí s pohotovostním uzlem. Úložiště je vhodné k poskytování sdílených složek, které jsou potřeba pro/sapmnt nebo globální transportní adresář SAP. Úložiště ANF přináší dostupnost funkcí, která je k dispozici jako nativní funkce NetApp.  



## <a name="azure-standard-ssd-storage"></a>Úložiště Azure Standard SSD
Ve srovnání s úložištěm Azure Standard Hard přináší úložiště Azure Standard SSD lepší dostupnost, konzistenci, spolehlivost a latenci. Je optimalizovaný pro úlohy, které vyžadují konzistentní výkon na nižších úrovních IOPS. Toto úložiště je minimální úložiště, které se používá pro neprodukční systémy SAP s nízkými IOPS a nároky na propustnost. Matrice schopností pro úlohu SAP vypadá takto:

| Schopnost| Komentář| Poznámky a odkazy | 
| --- | --- | --- | 
| Základní virtuální pevný disk operačního systému | vhodný přístup | jiné než produkční systémy |
| Datový disk | vhodný přístup | Některé neprodukční systémy s nízkými IOPS a požadavky na latenci |
| Adresář globálního přenosu SAP | NO | [Nepodporuje se](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | vhodný přístup | jiné než produkční systémy |
| Úložiště zálohování | vhodnou | - |
| Sdílené složky/sdílený disk | není dostupná | Vyžaduje třetí stranu |
| Odolnost | LRS, GRS | Pro disky nejsou k dispozici žádné ZRS |
| Latence | high | příliš vysoké pro globální transportní adresář SAP nebo produkční systémy |
| SLA PRO IOPS | NO | - |
| Maximální počet IOPS na disk | 500 | Nezávisle na velikosti disku |
| Smlouva SLA pro propustnost | NO | - |
| Certified HANA | NO | - |
| Možné snímky disku | ANO | - |
| Možné snímky Azure Backup VM | ANO | - |
| Náklady | NÍZKÁ | - |



**Shrnutí:** Úložiště Azure Standard SSD je minimální doporučení pro neprodukční virtuální počítače pro základní virtuální pevný disk, konečné nasazení DBMS s relativní latencí a/nebo nízké vstupně-výstupní operace a míry propustnosti. Tento typ úložiště Azure už není podporovaný pro hostování adresáře globálního přenosu SAP. 



## <a name="azure-standard-hdd-storage"></a>Úložiště Azure Standard HDD
Úložiště Azure HDD úrovně Standard jako jediný typ úložiště, když se v infrastruktuře Azure získala certifikace pro úlohy SAP NetWeaver v roce 2014. V roce 2014 byla virtuálním počítačům Azure velká a nízká propustnost úložiště. Proto by tento typ úložiště mohl zachovávat pouze požadavky. Úložiště je ideální pro úlohy necitlivé na latenci, které se špatně v prostoru SAP. Díky rostoucí propustnosti virtuálních počítačů Azure a zvýšenému zatížení těchto virtuálních počítačů se tento typ úložiště nepovažuje za použití se scénáři SAP. Matrice schopností pro úlohu SAP vypadá takto:

| Schopnost| Komentář| Poznámky a odkazy | 
| --- | --- | --- | 
| Základní virtuální pevný disk operačního systému | nevhodné | - |
| Datový disk | nevhodné | - |
| Adresář globálního přenosu SAP | NO | [Nepodporuje se](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | NO | Nepodporováno |
| Úložiště zálohování | vhodnou | - |
| Sdílené složky/sdílený disk | není dostupná | Vyžaduje soubory Azure nebo třetí stranu. |
| Odolnost | LRS, GRS | Pro disky nejsou k dispozici žádné ZRS |
| Latence | high | příliš vysoké pro použití DBMS, adresář globálního přenosu SAP nebo sapmnt/saploc |
| SLA PRO IOPS | NO | - |
| Maximální počet IOPS na disk | 500 | Nezávisle na velikosti disku |
| Smlouva SLA pro propustnost | NO | - |
| Certified HANA | NO | - |
| Možné snímky disku | ANO | - |
| Možné snímky Azure Backup VM | ANO | - |
| Náklady | NÍZKÁ | - |



**Shrnutí:** HDD úrovně Standard je typ úložiště Azure, který by se měl používat jenom k ukládání záloh SAP. Měla by se používat jenom jako základní virtuální pevný disk pro místo neaktivních systémů, jako jsou například vyřazené systémy používané pro vyhledávání dat zde a tam. Ale žádné aktivní vývojové a provozní virtuální počítače by měly být založené na tomto úložišti. Ani soubory databáze, které jsou hostovány v tomto úložišti


## <a name="azure-vm-limits-in-storage-traffic"></a>Omezení virtuálních počítačů Azure v provozu úložiště
V opačném případě se jedná o typ jednotlivého virtuálního počítače, který vyberete, hraje zásadní roli v šířce pásma úložiště, kterou můžete dosáhnout. U různých typů úložišť je potřeba vzít v úvahu:

| Typ úložiště| Linux | Windows | Komentáře |
| --- | --- | --- | --- |
| HDD úrovně Standard | [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md) | [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md) | Je nejspíš těžké dodotknout limity úložiště u středních nebo velkých virtuálních počítačů. |
| SSD úrovně Standard | [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md) | [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md) | Je nejspíš těžké dodotknout limity úložiště u středních nebo velkých virtuálních počítačů. |
| Premium Storage | [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md) | [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md) | Snadné navýšení IOPS nebo omezení propustnosti úložiště s konfigurací úložiště |
| Úložiště Ultra disk | [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md) | [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md) | Snadné navýšení IOPS nebo omezení propustnosti úložiště s konfigurací úložiště |
| Azure NetApp Files | [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md) | [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md) | Provoz úložiště používá šířku pásma propustnosti sítě a šířku pásma úložiště. |

V případě omezení si můžete všimnout, že:

- Čím menší je virtuální počítač, tím menší je počet disků, které můžete připojit. Toto neplatí pro ANF. Vzhledem k tomu, že se připojíte ke sdíleným složkám NFS nebo SMB, nebudete mít k připojení povolený limit počtu sdílených svazků.
- Virtuální počítače mají propustnost vstupně-výstupních operací a IOPS, které se dají snadno převažovat za disky Premium Storage a Ultra disks.
- V ANF přenos dat do sdílených svazků spotřebovává šířku pásma sítě virtuálního počítače a šířku pásma úložiště.
- U velkých svazků systému souborů NFS v TiB prostoru s dvojnásobnou přesností se bude propustnost přístupu takového svazku z jednoho virtuálního počítače stabilní úrovně na základě limitů systému Linux pro jednu relaci, která pracuje se sdíleným svazkem. 

Vzhledem k velikosti virtuálních počítačů Azure v životním cyklu systému SAP byste měli vyhodnotit limity IOPS a propustnosti úložiště nového a většího typu virtuálního počítače. V některých případech by taky mohlo být vhodné upravit konfiguraci úložiště na nové možnosti virtuálního počítače Azure. 


## <a name="striping-or-not-striping"></a>Prokládání nebo nepruhování
Vytvoření Stripe nastaveného na více discích Azure do jednoho většího svazku umožňuje nashromáždit IOPS a propustnost jednotlivých disků na jednom svazku. Používá se jenom pro Azure Storage úrovně Standard a Azure Premium Storage. Azure Ultra disk, na kterém můžete nakonfigurovat propustnost a IOPS nezávisle na kapacitě disku, nevyžaduje použití sad Stripe Sets. Sdílené svazky založené na systému souborů NFS nebo SMB nelze prokládat. Z důvodu nelineárního charakteru propustnosti služby Azure Premium Storage a IOPS můžete zřídit menší kapacitu se stejnými IOPS a propustností než velké samostatné disky Azure Premium Storage. To je metoda, která dosahuje vyšší propustnosti nebo IOPS s nižšími náklady pomocí Azure Premium Storage. Například rozložení na dva disky úložiště P15 úrovně Premium vám umožní následující propustnost: 

- 250 MiB/s. Tento svazek bude mít 512 GiBou kapacitu. Pokud chcete mít jeden disk, který vám poskytne 250 propustnosti MiB za sekundu, musíte vybrat P40 disk se 2 TiB kapacitou. 
- 400 MiB/s prokládá čtyři disky úložiště P10 úrovně Premium s celkovou kapacitou 512 GiB na základě Stripe. Pokud chcete mít jeden disk s minimální propustností 500 MiB za sekundu, museli byste vybrat disk úložiště P60 úrovně Premium s 8 TiB. Vzhledem k tomu, že náklady na Premium Storage jsou blízko lineárního využívání kapacity, můžete při používání Stripe vymezit úspory nákladů.

Při prokládaném je potřeba provést některá pravidla:

- Nemusíte použít žádné úložiště nakonfigurované na virtuálním počítači, protože Azure Storage uchovává data už redundantní.
- Disky, na které se sada Stripe používá, musí mít stejnou velikost.

Prokládání na více menších discích je nejlepším způsobem, jak dosáhnout dobrého poměru cen a výkonu pomocí Azure Premium Storage. Důrazně se rozumí, že proložení má ještě nějaké další nároky na nasazení a správu.

Konkrétní doporučení pro velikost Stripe najdete v dokumentaci pro různé systémy DBMS, například [SAP HANA konfigurací úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Další kroky
Přečtěte si články:

- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md)
- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](./hana-vm-operations-storage.md)
