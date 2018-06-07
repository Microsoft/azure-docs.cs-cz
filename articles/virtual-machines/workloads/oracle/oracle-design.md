---
title: Návrh a implementaci databáze Oracle na platformě Azure | Microsoft Docs
description: Návrh a implementaci k databázi Oracle v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: 2661c386ea747fc75b67df9a67c7e62ac8c4fea4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658134"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Návrh a implementace databáze Oracle v Azure

## <a name="assumptions"></a>Předpoklady

- Plánování migrace z místní databáze Oracle do Azure.
- Máte představu o různé metriky v sestavách Oracle AWR.
- Máte základní znalosti aplikace výkonu a využití platformy.

## <a name="goals"></a>Cíle

- Pochopit, jak optimalizovat Oracle nasazení v Azure.
- Prozkoumejte možností pro databázi Oracle v prostředí Azure ladění výkonu.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Rozdíly mezi místním a Azure implementace 

Toto jsou některé důležité věci, třeba mít na paměti, když jste migrace místní aplikace do Azure. 

Jeden důležitý rozdíl je, že v implementaci Azure prostředkům, například virtuálních počítačů, disků a virtuální sítě sdílejí mezi ostatní klienty. Kromě toho prostředky můžete omezeny na základě požadavků. Místo zaměřené na zamezení selhání provozu (MTBF), Azure zaměřují na zbývajících selhání (MTTR).

Následující tabulka uvádí některé rozdíly mezi místními implementace a implementaci Azure pro databázi Oracle.

> 
> |  | **Místní implementace** | **Azure implementace** |
> | --- | --- | --- |
> | **Sítě** |LAN NEBO WAN  |SDN (softwarově definované sítě)|
> | **Skupina zabezpečení** |Nástroje pro omezení adresy IP a portu |[Skupina zabezpečení sítě (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odolnost proti** |MTBF (střední čas mezi selhání) |MTTR (střední čas k obnovení)|
> | **Plánovaná údržba** |Opravy chyb a upgrady|[Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (opravy a upgrady spravovat přes Azure) |
> | **Prostředek** |Vyhrazený  |Sdílet s ostatními klienty|
> | **Oblasti** |Datová centra |[Dvojice oblast](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Úložiště** |Síť SAN nebo fyzické disky |[Spravovat Azure storage](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Škálování** |Vertikální Škálováním |Horizontální škálování|


### <a name="requirements"></a>Požadavky

- Určete rychlost velikosti a nárůst databáze.
- Určete požadavky IOPS, které můžete odhadnout na základě Oracle AWR sestavy nebo jiné sítě, nástroje pro sledování.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují čtyři potenciálních oblastí, které můžete vyladit ke zlepšení výkonu prostředí Azure:

- Velikost virtuálního počítače
- Propustnost sítě
- Typy disků a konfigurace
- Nastavení mezipaměti na disku

### <a name="generate-an-awr-report"></a>Generování sestavy AWR

Pokud máte existující databázi Oracle a plánování migrace do Azure, máte několik možností. Můžete spustit sestavy Oracle AWR metriky (IOPS, MB/s, GiBs a tak dále). Potom vyberte virtuální počítač podle metriky, které jste shromáždili. Nebo můžete kontaktujte tým infrastruktury podobné informace.

Můžete zvážit spouštění sestavy AWR během pravidelné a špičkovým úlohy, takže můžete porovnat. Na základě těchto zpráv, může Velikost virtuálních počítačů na základě průměrné zatížení nebo maximální zátěž.

Tady je příklad toho, jak vygenerovat zprávu o AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Klíčové metriky

Následují metriky, které můžete získat z AWR sestavy:

- Celkový počet jader
- Procesor o rychlosti
- Celkové paměti v GB
- Využití procesoru
- Rychlost přenosu dat ve špičce
- Počet vstupně-výstupních operací změny (čtení a zápis)
- Vrátit protokolu rychlost (MB/s)
- Propustnost sítě
- Míra latence sítě (dolní nebo horní)
- Velikost databáze v GB
- Bajtů přijatých prostřednictvím SQL * Net z/do klienta

### <a name="virtual-machine-size"></a>Velikost virtuálního počítače

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Odhad velikosti virtuálního počítače na základě využití procesoru, paměti a vstupu a výstupu ze sestavy AWR

Jednou z věcí, které může vypadat v je nejvyšší pět vypršel popředí události, které signalizují, kde jsou kritická místa systému.

V následujícím diagramu, například synchronizace souboru protokolu je v horní části. Znamená počet počká, které jsou požadovány, než LGWR zapíše protokolu vyrovnávací paměti do souboru protokolu operaci znovu. Tyto výsledky naznačují, že jsou lépe provádění úložiště nebo disky. Diagram navíc také zobrazuje počet procesor (jádra) a velikost paměti.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/cpu_memory_info.png)

Následující diagram znázorňuje celkový počet vstupně-výstupní operace čtení a zápisu. Existovaly 59 GB číst a zapisovat během doby sestavy 247.3 GB.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Vyberte virtuální počítač

Na základě informací shromážděných ze sestavy AWR, dalším krokem je vybrat virtuální počítač podobné velikosti, která vyhovuje vašim požadavkům. Seznam dostupných virtuálních počítačů najdete v článku [paměťově optimalizované](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Upřesnit nastavení velikosti virtuálních počítačů s řadu virtuálních počítačů podobné podle ACU

Po virtuálního počítače jste vybrali, věnujte pozornost ACU pro virtuální počítač. Můžete vybrat jiný virtuální počítač na základě hodnoty ACU, která lépe splňuje vaše požadavky. Další informace najdete v tématu [Azure výpočetní jednotky](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Snímek obrazovky stránky ACU jednotky](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Propustnost sítě

Následující diagram znázorňuje vztah mezi propustnost a IOPS:

![Snímek obrazovky propustnost](./media/oracle-design/throughput.png)

Celkovou šířku propustnost je odhadovaný podle následující informace:
- SQL * Net provoz
- MB/s x počet serverů (například Oracle Data Guard výstupní proud)
- Dalších faktorech, jako je například aplikace replikace

![Snímek obrazovky SQL * Net propustnost](./media/oracle-design/sqlnet_info.png)

Podle potřeb šířky pásma sítě, existují různé typy brány můžete vybírat. Mezi ně patří basic VpnGw a Azure ExpressRoute. Další informace najdete v tématu [brány VPN stránce s cenami](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recommendations** (Doporučení)

- Latence sítě vyšší ve srovnání s místním nasazením. Snižuje sítě zaokrouhlí služebních cest může výrazně zlepšit výkon.
- Pokud chcete zkrátit odezvy, konsolidovat aplikace, které mají vysokou transakce nebo "chatty" aplikací na jednom virtuálním počítači.

### <a name="disk-types-and-configurations"></a>Typy disků a konfigurace

- *Výchozí OS disky*: tyto typy disků nabízejí trvalých dat a ukládání do mezipaměti. Jsou optimalizované pro přístup k operační systém při spuštění a není určeno pro buď transakcí nebo datového skladu (analytical) úlohy.

- *Nespravované disky*: pomocí těchto typů disku spravovat účty úložiště, které ukládají soubory virtuálního pevného disku (VHD), které odpovídají vaše disky virtuálních počítačů. Soubory VHD jsou uloženy jako objekty BLOB stránky v účtech úložiště Azure.

- *Spravované disky*: spravuje účty úložiště, které používáte pro disky virtuálních počítačů Azure. Určete typ disku (premium nebo standard) a velikost disku, které potřebujete. Azure vytváří a spravuje disku za vás.

- *Disky úložiště Premium*: tyto typy disků jsou nejvhodnější pro úlohy v produkčním prostředí. Premium storage podporuje disky virtuálních počítačů, které lze připojit k určité virtuálních počítačů velikost series, jako je například řady DS, DSv2, GS a F virtuálních počítačů. Premium disk se dodává s jinou velikostí, a můžete si vybrat mezi disky rozsahu od 32 GB do 4096 GB. Velikost každého disku má svou vlastní specifikace výkonu. V závislosti na požadavcích vaší aplikace můžete jeden nebo více disků připojit k virtuálnímu počítači.

Když vytvoříte nový disk spravované z portálu, můžete **typ účtu** pro typ disku, kterou chcete použít. Mějte na paměti, že ne všechny dostupné disky jsou zobrazeny v rozevírací nabídce. Když vyberete konkrétní velikost virtuálního počítače, v nabídce zobrazuje pouze úložiště k dispozici premium SKU, které jsou založeny na velikost tohoto virtuálního počítače.

![Snímek obrazovky stránky spravovaných disků](./media/oracle-design/premium_disk01.png)

Další informace najdete v tématu [vysoce výkonné úložiště Premium a spravované disky pro virtuální počítače](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Po dokončení konfigurace úložiště na virtuálním počítači, můžete chtít načíst testování disky před vytvořením databáze. Znalost vstupně-výstupních operací míry z hlediska latence a propustnosti vám pomohou určit, pokud očekávaný propustnost s latencí cíle podporují virtuální počítače.

Existuje několik nástrojů pro zatížení testování aplikací, jako je Oracle Orion, Sysbench a Fio.

Znovu spusťte zátěžový test, poté, co nasadíte databázi Oracle. Spuštění úlohy obyčejnými a špičkovým a výsledky zobrazeny směrného plánu vašeho prostředí.

Může to být víc důležité velikost úložiště založené na rychlost, jakou IOPS spíše než velikost úložiště. Například pokud požadovaná IOPS je 5 000, ale potřebujete jenom 200 GB, může stále získáte disku P30 třída premium i když se dodává s více než 200 GB úložiště.

Rychlost, jakou IOPS je možné získat ze sestavy AWR. Je dáno protokolu opakování, fyzických čtení a zápisů rychlost.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/awr_report.png)

Například velikost opakování je 12,200,000 bajtů za sekundu, které se rovná 11.63 MB/s.
IOPS se 12,200,000 / 2,358 = 5,174.

Až budete mít přehledné informace o vstupně-výstupní požadavky, můžete zvolit kombinaci jednotek, které jsou nejvhodnější pro naplnění těchto požadavků.

**Recommendations** (Doporučení)

- Pro data tabulkového prostoru, rozloženy vstupně-výstupní úlohy počet disků pomocí úložiště spravovaný nebo Oracle ASM.
- Jak pro operace náročné na čtení a zápisu náročných zvyšuje velikost bloku vstupně-výstupních operací, přidejte další datové disky.
- Zvětšete velikost bloku u velkých sekvenčních procesů.
- Komprese dat použijte ke snížení vstupně-výstupní operace (pro data a indexů).
- Oddělené opakování protokoly, systém a temps a vrátit zpět TS na samostatné datové disky.
- Umístěte všechny soubory aplikace na výchozí disky operačního systému (/ dev/sda). Tyto disky nejsou optimalizovány pro rychlé virtuální počítač nemusí časy spuštění a poskytují dobrý výkon pro vaši aplikaci.

### <a name="disk-cache-settings"></a>Nastavení mezipaměti na disku

Existují tři možnosti pro ukládání do mezipaměti hostitele:

- *Jen pro čtení*: všechny požadavky jsou uložená v mezipaměti pro budoucí čtení. Všech zápisů jsou nastavené jako trvalé přímo do Azure Blob storage.

- *Čtení a zápis*: Toto je "čtení napřed" algoritmu. Čtení a zápisu jsou uložená v mezipaměti pro budoucí čtení. Non zápisu prostřednictvím zápisů jsou trvalé nejprve do místní mezipaměti. Pro systém SQL Server zůstávají zápisy do úložiště Azure, protože používá přímým zápisem. Také poskytuje nejnižší latenci disku pro lehké úlohy.

- *Žádný* (zakázáno): pomocí této možnosti můžete obejít mezipaměti. Všechna data převede na disk a trvalé do služby Azure Storage. Tato metoda poskytuje nejvyšší rychlost vstupně-výstupních operací, pro zatížení s intenzivním vstupně-výstupních operací. Musíte také vzít v úvahu "transakce náklady".

**Recommendations** (Doporučení)

Chcete-li maximalizovat propustnost, doporučujeme začínat **žádné** pro použití mezipaměti u hostitele. Pro Storage úrovně Premium, mějte "překážek" je nutné zakázat, když připojíte pomocí systému souborů **jen pro čtení** nebo **žádné** možnosti. Aktualizujte soubor /etc/fstab s UUID na discích.

Další informace najdete v tématu [Premium úložiště pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Snímek obrazovky stránky spravovaných disků](./media/oracle-design/premium_disk02.png)

- Pro disky operačního systému, použít výchozí **pro čtení a zápis** ukládání do mezipaměti.
- Pro systém, TEMP a vrácení zpět pomocí **žádné** pro ukládání do mezipaměti.
- Pro DATA, použijte **žádné** pro ukládání do mezipaměti. Ale pokud vaše databáze je jen pro čtení nebo náročné na čtení, použijte **jen pro čtení** ukládání do mezipaměti.

Po uložení nastavení disku vaše data nelze změnit nastavení mezipaměti hostitele, pokud se odpojit disk na úrovni operačního systému a znovu připojte po provedení změn.


## <a name="security"></a>Zabezpečení

Po nastavení a konfiguraci prostředí Azure, dalším krokem je zabezpečení sítě. Tady jsou některá doporučení:

- *Zásady skupiny NSG*: NSG může být definovaná podsíť nebo síťový adaptér. Je jednodušší pro řízení přístupu na úrovni podsítě jak pro zabezpečení a vynutit směrování pro věcmi, jako jsou brány firewall pro aplikaci.

- *Jumpbox*: lépe zabezpečeného přístupu správci nesmí připojovat přímo k služba aplikace nebo databáze. Jumpbox slouží jako média mezi počítačem správce a prostředky Azure.
![Snímek obrazovky stránky Jumpbox topologie](./media/oracle-design/jumpbox.png)

    Počítač správce by měl nabízejí IP omezený přístup jenom jumpbox. Jumpbox mají mít přístup k aplikaci a databázi.

- *Privátní sítě* (podsítě): doporučujeme mít aplikace služby a databáze v samostatných podsítích v tak lepší řízení lze nastavit pomocí zásad skupiny NSG.


## <a name="additional-reading"></a>Další čtení

- [Konfigurace Oracle ASM](configure-oracle-asm.md)
- [Konfigurace Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurace brány Golden Oracle](configure-oracle-golden-gate.md)
- [Oracle zálohování a obnovení](oracle-backup-recovery.md)

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vysoce dostupné virtuální počítače](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky rozhraní příkazového řádku Azure nasazení virtuálních počítačů](../../linux/cli-samples.md)
