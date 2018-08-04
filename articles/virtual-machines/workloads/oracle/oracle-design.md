---
title: Návrh a implementace databáze Oracle v Azure | Dokumentace Microsoftu
description: Návrh a implementace databáze Oracle ve vašem prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: d4c0bbdfb1afcef33727ba4b5b432c5de79168d4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495216"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Návrh a implementace databáze Oracle v Azure

## <a name="assumptions"></a>Předpoklady

- Plánování migrace z místní databáze Oracle do Azure.
- Pochopení různých metrik máte v sestavách Oracle AWR.
- Máte znalosti směrný plán výkonu aplikace a využití platformy.

## <a name="goals"></a>Cíle

- Naučte se optimalizovat vaše nasazení Oracle v Azure.
- Prozkoumejte možnosti pro Oracle database v prostředí Azure pro optimalizaci výkonu.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Rozdíly mezi místním a implementace Azure 

Toto jsou některé důležité věci potřeba mít na paměti, když probíhá migrace místních aplikací do Azure. 

Jeden důležitý rozdíl je, že v implementaci Azure prostředky, jako jsou virtuální počítače, disky a virtuální sítě jsou odkazy sdíleny mezi ostatní klienty. Kromě toho prostředky můžu omezit na základě požadavků. Místo zaměření na zabránění selháním (MTBF), Azure víc zaměřené na zbývajících selhání (MTTR).

V následující tabulce jsou uvedeny některé rozdíly mezi místní implementace a Azure implementace databáze Oracle.

> 
> |  | **Místní implementace** | **Implementace Azure** |
> | --- | --- | --- |
> | **Sítě** |LAN/WAN  |SDN (software softwarově definované sítě)|
> | **Skupina zabezpečení** |Omezení IP adresa/port nástroje |[Skupina zabezpečení sítě (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odolnost** |MTBF (střední doby mezi poruchami) |MTTR (průměrný čas potřebný k obnovení)|
> | **Plánovaná údržba** |Opravy a upgrady|[Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (opravy a upgrady spravuje Azure) |
> | **Prostředek** |Vyhrazený  |Sdílet s ostatními klienty|
> | **Oblasti** |Datová centra |[Párování oblastí](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Úložiště** |Síť SAN nebo fyzických disků |[Spravované v Azure storage](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Škálování** |Vertikální škálování |Horizontální škálování|


### <a name="requirements"></a>Požadavky

- Určete rychlost velikosti a nárůst databáze.
- Určení požadavků na vstupně-výstupních operací, které chcete-li odhadnout na základě Oracle AWR sestavy nebo jiné nástroje pro monitorování sítě.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují čtyři potenciální oblasti, které můžete ladit ke zlepšení výkonu prostředí Azure:

- Velikost virtuálního počítače
- Propustnost sítě
- Typy disků a konfigurace
- Nastavení mezipaměti disku

### <a name="generate-an-awr-report"></a>Generování sestavy AWR

Pokud máte existující databázi Oracle a plánujete migrovat do Azure, máte několik možností. Můžete spustit sestavy Oracle AWR a získat metriky (vstupně-výstupních operací, MB/s, GiBs a tak dále). Potom vyberte virtuální počítač na základě metrik, které jste shromáždili. Nebo můžete kontaktovat infrastruktury týmem pro získání podobné informace.

Můžete zvážit spouštění sestavy AWR během pravidelné a špičku úloh, abyste mohli porovnat. Na základě těchto sestav, můžete velikost virtuálních počítačů na základě průměrné zatížení nebo maximálního zatížení.

Tady je příklad toho, jak vygenerovat sestavu AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Klíčové metriky

Metriky, které můžete získat z AWR sestavy jsou následující:

- Celkový počet jader
- Rychlost procesoru
- Celková paměť v GB
- Využití procesoru
- Rychlost přenosu dat ve špičce
- Frekvence změn vstupně-výstupních operací (čtení a zápis)
- Znovu frekvence protokolu (MB/s)
- Propustnost sítě
- Míra latence sítě (nízká/vysoká)
- Velikost databáze v GB
- Počet bajtů přijatých prostřednictvím SQL * Net z/do klienta

### <a name="virtual-machine-size"></a>Velikost virtuálního počítače

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Odhad velikosti virtuálního počítače na základě využití procesoru, paměti a vstupně-výstupní operace z AWR sestavy

Jednou z věcí, podívejte se na je hlavní pět popředí vypršel časový limit události, které označují, kde jsou problémová místa systému.

V následujícím diagramu, například synchronizace souboru protokolu je v horní části. Označuje počet počká, které jsou požadovány, než LGWR zapisuje do souboru protokolu znovu protokolu vyrovnávací paměti. Tyto výsledky označuje, že se vyžadují lépe uskutečnitelný úložiště nebo disky. Kromě toho diagram také ukazuje počet procesorů (jader) a množství paměti.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/cpu_memory_info.png)

Následující diagram znázorňuje celkový počet vstupně-výstupních operací čtení a zápisu. Došlo k 59 GB, přečtěte si a 247.3 napsané v době sestavy.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Vyberte virtuální počítač

Na základě informací, které jste shromáždili ze sestavy AWR, dalším krokem je vybrat virtuální počítač stejné velikosti, která splňuje vaše požadavky. Seznam dostupných virtuálních počítačů najdete v článku [paměťově optimalizované](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Vyladění velikosti virtuálního počítače s podobné řadu virtuálních počítačů podle ACU

Poté, co jste se rozhodli virtuální počítač, věnujte pozornost ACU pro virtuální počítač. Můžete zvolit jiný virtuální počítač na základě hodnoty ACU, která lépe splňuje vaše požadavky. Další informace najdete v tématu [výpočetní jednotky Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Snímek obrazovky stránky ACU jednotky](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Propustnost sítě

Následující diagram znázorňuje vztah mezi propustnost a vstupně-výstupních operací:

![Snímek obrazovky s propustností](./media/oracle-design/throughput.png)

Propustnost sítě celkový počet je odhad na základě následujících informací:
- SQL * Net provozu
- MB/s x počet serverů (jako je Oracle Data Guard výstupní proud)
- Dalších faktorů, jako je například aplikace replikace

![Snímek obrazovky nástroje SQL * Net propustnost](./media/oracle-design/sqlnet_info.png)

Podle potřeby šířky pásma sítě, jsou různé typy brány můžete vybírat. Patří mezi ně basic VpnGw a Azure ExpressRoute. Další informace najdete v tématu [stránce s cenami za bránu VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Recommendations** (Doporučení)

- Latence sítě vyšší ve srovnání s místním nasazením. Omezení sítě round zkracuje dobu odezvy může výrazně zlepšit výkon.
- Pokud chcete zkrátit dobu odezvy, konsolidovat aplikace, které mají vysokou transakce nebo "příliš upovídaným" aplikací na stejný virtuální počítač.

### <a name="disk-types-and-configurations"></a>Typy disků a konfigurace

- *Výchozí disky s operačním systémem*: těchto typů disků nevidí nabízejí trvalých dat a ukládání do mezipaměti. Jsou optimalizované pro přístup k operační systém při spuštění a nejsou určeny pro buď transakční nebo datového skladu (analytické) úlohy.

- *Nespravované disky*: pomocí těchto typů disků nevidí spravovat účty úložiště, které ukládají soubory virtuálního pevného disku (VHD), které odpovídají disky virtuálních počítačů. Soubory virtuálního pevného disku se ukládají jako objekty BLOB stránky v účtech úložiště Azure.

- *Spravované disky*: spravuje účty úložiště, které používáte pro disky virtuálních počítačů Azure. Zadejte typ disku (premium nebo standard) a velikost disku, které potřebujete. Azure vytvoří a spravuje disk za vás.

- *Disky storage úrovně Premium*: těchto typů disků nevidí se nejlíp hodí pro úlohy v produkčním prostředí. Storage úrovně Premium podporuje disky virtuálních počítačů, které lze připojit na konkrétní virtuální počítače velikost series, jako je například řady DS, DSv2, GS a F virtuálních počítačů. Disk úrovně premium se dodává s jinou velikostí, a můžete si vybrat mezi disky od 32 GB do 4 096 GB. Velikost každého disku má svůj vlastní specifikace výkonu. V závislosti na požadavcích vaší aplikace můžete připojit jeden nebo víc disků k virtuálnímu počítači.

Při vytváření nového spravovaného disku z portálu, můžete použít **typ účtu** pro typ disku, kterou chcete použít. Mějte na paměti, že ne všechny dostupné disky jsou uvedeny v rozevírací nabídce. Po zvolení konkrétní velikosti virtuálního počítače, v nabídce se zobrazuje pouze storage úrovně premium dostupné skladové položky, které jsou založeny na danou velikost virtuálního počítače.

![Snímek obrazovky stránky spravovaného disku](./media/oracle-design/premium_disk01.png)

Další informace najdete v tématu [vysoce výkonné úložiště úrovně Premium a spravovaným diskům pro virtuální počítače](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Po dokončení konfigurace úložiště na virtuálním počítači, můžete chtít načíst testování disky před vytvořením databáze. Znalost, rychlost vstupně-výstupní operace z hlediska latence a propustnosti vám může pomoct zjistit, jestli virtuální počítače podporují očekávané propustnosti s cíli latence.

Existují různé nástroje pro testování zatížení aplikace, jako je Oracle Orion Sysbench a Fio.

Spusťte zátěžový test znovu po nasazení databáze Oracle. Spuštění úloh pravidelné a špičku a ve výsledcích zobrazí, základních hodnot v prostředí.

Může být mnohem důležitější pro nastavení velikosti úložiště založené na rychlost vstupně-výstupních operací než velikost úložiště. Například požadované vstupně-výstupních operací je 5 000, ale potřebujete jenom 200 GB, vám může dostanete, i když disk úrovně premium P30 třídy i v případě, že obsahuje více než 200 GB úložiště.

Rychlost vstupně-výstupních operací je získat z AWR sestavy. Je určen log znovu, fyzických čtení a zápisy míry.

![Snímek obrazovky stránky sestavy AWR](./media/oracle-design/awr_report.png)

Například velikost znovu je 12,200,000 bajtů za sekundu, které se rovná 11.63 MB/s.
IOPS se 12,200,000 / 2,358 = 5,174.

Jakmile budete mít jasnou představu o vstupně-výstupní požadavky, můžete zvolit kombinaci jednotek, které jsou nejvhodnější pro naplnění těchto požadavků.

**Recommendations** (Doporučení)

- Pro tabulkový datový prostor rozložil zatížení vstupně-výstupních operací na počet disků s použitím spravovaného úložiště nebo Oracle ASM.
- Jak se zvyšuje velikost bloku vstupně-výstupních operací pro operace náročné na čtení a náročné na zápis, přidáte další datové disky.
- Zvětšete velikost bloku pro sekvenční velké procesy.
- Používejte kompresi dat ke snížení vstupně-výstupních operací (pro data a indexů).
- Samostatné protokoly znovu, systém a podmínky a zpět TS na samostatné datové disky.
- Neumisťujte žádných souborů aplikací na výchozí disky operačního systému (/ dev/sda). Tyto disky nejsou optimalizovány pro virtuální počítač rychlé spuštění a jejich nemusí poskytnout dostatečný výkon pro vaše aplikace.

### <a name="disk-cache-settings"></a>Nastavení mezipaměti disku

Existují tři možnosti pro použití mezipaměti u hostitele:

- *Jen pro čtení*: všechny požadavky jsou uložené v mezipaměti pro budoucí čtení. Všechny zápisy jsou trvalé přímo do Azure Blob storage.

- *Čtení a zápis*: Toto je "čtení napřed" algoritmu. Čtení a zápisu jsou uložené v mezipaměti pro budoucí čtení. Zápisy non přímého zápisu jsou trvalé nejprve do místní mezipaměti. Pro SQL Server zůstávají zápisy do služby Azure Storage, protože používá přímého zápisu. Také poskytuje nejnižší latenci disku pro malé úlohy.

- *Žádný* (zakázáno): pomocí této možnosti můžete obejít mezipaměť. Všechna data je převedena na disk a ukládají do Azure Storage. Tato metoda nabízí nejvyšší sazba vstupně-výstupních operací pro úlohy náročné na vstupně-výstupních operací. Také je potřeba vzít v úvahu "transakční náklady".

**Recommendations** (Doporučení)

Pro maximalizaci propustnosti, doporučujeme začít s **žádný** pro použití mezipaměti u hostitele. Pro Premium Storage, mějte "překážky" je nutné zakázat, když připojíte pomocí systému souborů **jen pro čtení** nebo **žádný** možnosti. Aktualizujte soubor /etc/fstab s UUID disky.

Další informace najdete v tématu [Storage úrovně Premium pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Snímek obrazovky stránky spravovaného disku](./media/oracle-design/premium_disk02.png)

- Pro disky s operačním systémem, použijte výchozí **r/w** ukládání do mezipaměti.
- Pro systém, TEMP a vrácení zpět pomocí **žádný** pro ukládání do mezipaměti.
- Pro DATA, použijte **žádný** pro ukládání do mezipaměti. Ale pokud vaše databáze je jen pro čtení nebo pro intenzivní čtení, použijte **jen pro čtení** ukládání do mezipaměti.

Po uložení nastavení disku dat nelze změnit nastavení mezipaměti hostitele, není-li odpojit jednotku na úrovni operačního systému a znovu ho připojte po provedení změny.


## <a name="security"></a>Zabezpečení

Dalším krokem po nastavení a nakonfigurovat prostředí Azure je zabezpečení sítě. Zde je několik doporučení:

- *Zásady skupiny zabezpečení sítě*: Skupina zabezpečení sítě může definovat podsítě a síťový adaptér Je jednodušší pro řízení přístupu na úrovni podsítě, jak pro zabezpečení a vynucené směrování pro takové věci, jako jsou brány firewall aplikací.

- *Jumpbox*: lépe zabezpečit přístup, Správce by neměl připojit přímo k aplikační služby nebo databáze. Jumpbox slouží jako média mezi správce počítače a prostředky Azure.
![Snímek obrazovky stránky Jumpbox topologie](./media/oracle-design/jumpbox.png)

    Počítač správce by měl nabídnout IP omezený přístup k jumpboxu pouze. Jumpbox má přístup k aplikaci a databázi.

- *Privátní síť* (podsítě): doporučujeme, aby aplikace služby a databáze v oddělených podsítích v tak lepší kontrolu můžete nastavit pomocí zásad skupiny zabezpečení sítě.


## <a name="additional-reading"></a>Další čtení

- [Konfigurace Oracle ASM](configure-oracle-asm.md)
- [Konfigurace Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurace Oracle Golden brány](configure-oracle-golden-gate.md)
- [Oracle zálohování a obnovení](oracle-backup-recovery.md)

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vysoce dostupných virtuálních počítačů](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky nasazení virtuálního počítače pomocí Azure CLI](../../linux/cli-samples.md)
