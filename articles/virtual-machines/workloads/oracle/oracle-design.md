---
title: Návrh a implementace databáze Oracle v Azure | Microsoft Docs
description: Navrhněte a implementujte databázi Oracle v prostředí Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 6b7c280d9ff5f4d8a3c35eb11e080bf2f9f287c0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959165"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Návrh a implementace databáze Oracle v Azure

## <a name="assumptions"></a>Předpoklady

- Chystáte se migrovat databázi Oracle z místního prostředí do Azure.
- Máte [balíček diagnostiky](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) pro Oracle Database, které chcete migrovat.
- V sestavách Oracle AWR rozumíte nejrůznějším metrikám.
- Máte základní znalosti o výkonu aplikací a využití platforem.

## <a name="goals"></a>Cíle

- Pochopte, jak optimalizovat nasazení Oracle v Azure.
- Prozkoumejte možnosti ladění výkonu pro databázi Oracle v prostředí Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Rozdíly mezi místním nasazením a implementací Azure 

Níže jsou uvedeny některé důležité věci, které je potřeba vzít v úvahu při migraci místních aplikací do Azure. 

Jedním z důležitých rozdílů je to, že v implementaci Azure se prostředky, jako jsou virtuální počítače, disky a virtuální sítě, sdílí mezi ostatními klienty. Prostředky je navíc možné omezit na základě požadavků. Místo toho, abyste se zazaměřili na selhání (MTBF), se Azure podrobněji zaměřuje na zbývající část (MTTR).

V následující tabulce jsou uvedeny některé rozdíly mezi místními implementacemi a implementací Azure databáze Oracle.


|  | Místní implementace | Implementace Azure |
| --- | --- | --- |
| **Sítě** |LAN/WAN  |SDN (softwarově definované sítě)|
| **Skupina zabezpečení** |Nástroje pro omezení IP adres nebo portů |[Skupina zabezpečení sítě (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Odolnost** |MTBF (střední doba mezi selháními) |MTTR (Průměrná doba obnovení)|
| **Plánovaná údržba** |Opravy a upgrady|[Skupiny dostupnosti](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (opravy a upgrady spravované přes Azure) |
| **Prostředek** |Vyhrazená  |Sdíleno s ostatními klienty|
| **Oblasti** |Datová centra |[Párování oblastí](../../regions.md#region-pairs)|
| **Storage** |SÍŤ SAN/fyzické disky |[Úložiště spravované v Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Škálování** |Vertikální škálování |Horizontální škálování|


### <a name="requirements"></a>Požadavky

- Určete velikost databáze a míru růstu.
- Určete požadavky na IOPS, které můžete odhadnout na základě sestav Oracle AWR nebo jiných nástrojů pro monitorování sítě.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují čtyři možné oblasti, které můžete vyladit, aby se zlepšil výkon v prostředí Azure:

- Velikost virtuálního počítače
- Propustnost sítě
- Typy disků a konfigurace
- Nastavení diskové mezipaměti

### <a name="generate-an-awr-report"></a>Generování sestavy AWR

Pokud máte existující databázi Oracle a plánujete migrovat na Azure, máte několik možností. Pokud máte [diagnostické sady](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) pro instance Oracle, můžete spuštěním sestavy Oracle AWR získat metriky (IOPS, MB/s, GiBs a tak dále). Pak zvolte virtuální počítač na základě metrik, které jste shromáždili. Případně se můžete obrátit na tým infrastruktury a získat podobné informace.

V rámci pravidelných i špičkových úloh můžete zvážit spuštění sestavy AWR, abyste je mohli porovnat. Na základě těchto sestav můžete virtuální počítače měnit na základě průměrné zátěže nebo maximálního zatížení.

Následuje příklad generování sestavy AWR (generování sestav AWR pomocí správce Oracle Enterprise Manageru, pokud vaše aktuální instalace obsahuje):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Klíčové metriky

Níže jsou uvedené metriky, které můžete získat ze sestavy AWR:

- Celkový počet jader
- Taktová rychlost procesoru
- Celková paměť v GB
- Využití procesoru
- Rychlost přenosu dat ve špičce
- Frekvence změn v/v (čtení a zápis)
- Rychlost opakování protokolu (MB/s)
- Propustnost sítě
- Frekvence latence sítě (nízká/vysoká)
- Velikost databáze v GB
- Bajty přijaté přes SQL * NET od/do klienta

### <a name="virtual-machine-size"></a>Velikost virtuálního počítače

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. odhad velikosti virtuálního počítače na základě využití procesoru, paměti a vstupně-výstupních operací ze sestavy AWR

Jednou z věcí, které byste mohli nahlížet, je prvních pět událostí s vypršenou platností na popředí, které označují, kde jsou systémové body kritické.

Například v následujícím diagramu je synchronizace souborů protokolu v horní části. Označuje počet čekání, které jsou požadovány před tím, než LGWR zapíše vyrovnávací paměť protokolu do souboru protokolu opětovného odeslání. Tyto výsledky naznačují, že je potřeba úložiště nebo disky lépe provádět. Kromě toho diagram zobrazuje také počet PROCESORů (jader) a množství paměti.

![Snímek obrazovky zobrazující synchronizaci souboru protokolu v horní části tabulky](./media/oracle-design/cpu_memory_info.png)

Následující diagram znázorňuje celkový počet vstupně-výstupních operací čtení a zápisu. Během sestavy bylo zapsáno 59 GB a 247,3 GB v době sestavování.

![Snímek obrazovky, který zobrazuje celkový počet vstupně-výstupních operací čtení a zápisu.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Volba virtuálního počítače

Na základě informací, které jste shromáždili ze sestavy AWR, je dalším krokem výběr virtuálního počítače podobné velikosti, která splňuje vaše požadavky. Seznam dostupných virtuálních počítačů najdete v paměti v článku [optimalizované pro paměť](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. vyladění velikosti virtuálního počítače pomocí podobné série virtuálních počítačů na základě ACU

Po výběru virtuálního počítače věnujte pozornost ACU pro virtuální počítač. Můžete zvolit jiný virtuální počítač na základě hodnoty ACU, která lépe vyhovuje vašim požadavkům. Další informace najdete v tématu [výpočetní jednotka Azure](../../acu.md).

![Snímek obrazovky se stránkou jednotek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Propustnost sítě

Následující diagram znázorňuje vztah mezi propustností a IOPS:

![Snímek obrazovky s propustností](./media/oracle-design/throughput.png)

Celková propustnost sítě se odhadne na základě těchto informací:
- SQL * síťový provoz
- MB/s × počet serverů (odchozí datový proud, jako je Oracle data Guard)
- Další faktory, jako je například replikace aplikací

![Snímek obrazovky SQL * NET propustnost](./media/oracle-design/sqlnet_info.png)

Na základě požadavků na šířku pásma sítě si můžete vybrat z různých typů bran. Mezi ně patří Basic, VpnGw a Azure ExpressRoute. Další informace najdete na stránce s [cenami služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Doporučení**

- V porovnání s místním nasazením je latence sítě vyšší. Snížení zatížení sítě může výrazně zlepšit výkon.
- Pro omezení zpátečních cest Konsolidujte aplikace, které mají vysoké transakce nebo "konverzace" na stejném virtuálním počítači.
- Pro lepší výkon sítě používejte Virtual Machines s [akcelerovanými síťovými](../../../virtual-network/create-vm-accelerated-networking-cli.md) službami.
- U některých distribucí systému Linux zvažte možnost povolit [podporu pro funkci trim a](../../linux/configure-lvm.md#trimunmap-support)oddálení.
- Nainstalujte [správce Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) do samostatného virtuálního počítače.
- Ve výchozím nastavení nejsou v systému Linux povoleny velké stránky. Zvažte možnost Povolit velké stránky a nastavit `use_large_pages = ONLY` Oracle DB. To může přispět ke zvýšení výkonu. Další informace najdete [tady](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy disků a konfigurace

- *Výchozí disky s operačním systémem*: tyto typy disků nabízejí trvalá data a ukládání do mezipaměti. Jsou optimalizované pro přístup k operačnímu systému při spuštění a nejsou navržené pro transakční data ani pro úlohy datového skladu (analytická data).

- *Nespravované disky*: u těchto typů disků spravujete účty úložiště, které ukládají soubory virtuálních pevných disků (VHD), které odpovídají vašim DISKŮM virtuálních počítačů. Soubory VHD se ukládají jako objekty blob stránky v účtech úložiště Azure.

- *Spravované disky*: Azure spravuje účty úložiště, které používáte pro disky virtuálních počítačů. Určíte typ disku (Premium nebo Standard) a velikost disku, který potřebujete. Azure vytvoří a spravuje disk za vás.

- *Disky Premium Storage*: tyto typy disků jsou nejvhodnější pro produkční úlohy. Premium Storage podporuje disky virtuálních počítačů, které je možné připojit ke konkrétním virtuálním počítačům řady velikostí, například k virtuálním počítačům DS, DSv2, GS a F Series. Disk Premium se dodává s různými velikostmi a můžete si vybrat mezi disky od 32 do 4 096 GB. Každá velikost disku má své vlastní specifikace výkonu. V závislosti na požadavcích vaší aplikace můžete k VIRTUÁLNÍmu počítači připojit jeden nebo více disků.

Při vytváření nového spravovaného disku z portálu můžete zvolit **typ účtu** pro typ disku, který chcete použít. Pamatujte, že ne všechny dostupné disky se zobrazí v rozevírací nabídce. Po zvolení konkrétní velikosti virtuálního počítače se v nabídce zobrazí jenom dostupné SKU Premium Storage, které jsou založené na velikosti tohoto virtuálního počítače.

![Snímek stránky spravovaného disku](./media/oracle-design/premium_disk01.png)

Po konfiguraci úložiště na VIRTUÁLNÍm počítači můžete před vytvořením databáze zátěž testů načíst. Znalost vstupně-výstupních přenosů z hlediska latence a propustnosti vám může pomoci určit, jestli virtuální počítače podporují očekávanou propustnost s cíli latence.

Existuje řada nástrojů pro zátěžové testování aplikací, jako je například Oracle Orion, Sysbench a FIO.

Spusťte test zatížení znovu po nasazení databáze Oracle. Zahajte pravidelné a špičkové úlohy a výsledky vám ukáže základní údaje o vašem prostředí.

Je možné, že velikost úložiště bude důležitější na základě sazby za IOPS místo velikosti úložiště. Pokud je například požadovaný IOPS 5 000, ale potřebujete pouze 200 GB, můžete i nadále získat disk třídy P30 na úrovni Premium, i když obsahuje více než 200 GB úložiště.

Frekvence IOPS se dá získat ze sestavy AWR. Určuje se v protokolu opětovného načtení, fyzických čtení a rychlosti zápisu.

![Snímek obrazovky se stránkou sestavy AWR](./media/oracle-design/awr_report.png)

Například velikost opakování je 12 200 000 bajtů za sekundu, což se rovná 11,63 MB/s.
IOPS je 12 200 000/2 358 = 5 174.

Po jasném přehledu požadavků na vstupně-výstupní operace můžete zvolit kombinaci jednotek, které jsou nejvhodnější pro splnění těchto požadavků.

**Doporučení**

- V případě datového tabulkového úložiště Rozšiřte vstupně-výstupní úlohy napříč několika disky pomocí spravovaného úložiště nebo Oracle ASM.
- Jak velikost bloku vstupu/výstupu roste pro operace náročné na čtení a náročné na zápis, přidejte další datové disky.
- Zvětšete velikost bloku pro velké sekvenční procesy.
- Komprese dat slouží k omezení vstupně-výstupních operací (pro data i indexy).
- Oddělte protokoly znovu, systém a dočasné soubory a vraťte se do služby TS TS na samostatných datových discích.
- Neumísťujte žádné soubory aplikace na výchozí disky s operačním systémem (/dev/sda). Tyto disky nejsou optimalizované pro rychlé spouštění virtuálních počítačů a nemusí pro vaši aplikaci poskytovat dobrý výkon.
- Při použití virtuálních počítačů řady M-Series na Premium Storage povolte [akcelerátor zápisu](../../how-to-enable-write-accelerator.md) na disku znovu protokoly.

### <a name="disk-cache-settings"></a>Nastavení diskové mezipaměti

Pro ukládání do mezipaměti hostitele existují tři možnosti:

- *ReadOnly*: všechny požadavky jsou ukládány do mezipaměti pro budoucí čtení. Všechna zápisy se ukládají přímo do Azure Blob Storage.

- Probuzení *: jedná* se o algoritmus "Read-to-dopředu". Čtení a zápisy jsou ukládány do mezipaměti pro budoucí čtení. Zápisy bez zápisů jsou nejprve trvale ukládány do místní mezipaměti. Poskytuje taky nejnižší latenci disku pro úlohy s nižšími procesy. Použití mezipaměti s podporou přečtení z aplikace, která nezpracovává trvalá potřebná data, může způsobit ztrátu dat, pokud dojde k chybě virtuálního počítače.

- *None* (zakázáno): pomocí této možnosti můžete mezipaměť obejít. Všechna data se přenesou na disk a trvale Azure Storage. Tato metoda poskytuje nejvyšší počet vstupně-výstupních operací pro úlohy náročné na vstupně-výstupní operace. Také je nutné vzít v úvahu "náklady na transakci".

**Doporučení**

Pro maximalizaci propustnosti doporučujeme začít s **možnostmi žádná** pro ukládání do mezipaměti hostitele. V případě Premium Storage Pamatujte na to, že při připojování systému souborů s možnostmi **ReadOnly** nebo **none** je nutné zakázat "překážky". Aktualizujte soubor/etc/fstab s identifikátorem UUID na disky.

![Snímek stránky spravovaného disku, který zobrazuje možnosti ReadOnly a None](./media/oracle-design/premium_disk02.png)

- Pro disky s operačním systémem použijte výchozí mezipaměť **pro čtení a zápis** .
- U možnosti SYSTEM, TEMP a UNDO použijte **možnost None** pro ukládání do mezipaměti.
- Pro DATA použijte **možnost None** pro ukládání do mezipaměti. Pokud je ale databáze jen pro čtení nebo je náročná na čtení, používejte ukládání do mezipaměti **jen pro čtení** .

Po uložení nastavení datového disku nemůžete nastavení mezipaměti hostitele změnit, pokud nepřipojíte jednotku na úrovni operačního systému a pak ji znovu připojíte po provedení změny.

## <a name="security"></a>Zabezpečení

Po nastavení a konfiguraci prostředí Azure je dalším krokem zabezpečení vaší sítě. Tady je několik doporučení:

- *Zásady NSG*: NSG se dají definovat v podsíti nebo síťové kartě. Je jednodušší řídit přístup na úrovni podsítě, a to jak pro zabezpečení, tak pro vynucení směrování pro věci, jako jsou brány firewall pro aplikace.

- *JumpBox*: pro bezpečnější přístup by se správci neměli přímo připojovat ke službě Application Service nebo databázi. JumpBox se používá jako médium mezi počítačem správce a prostředky Azure.
![Snímek obrazovky se stránkou topologie JumpBox](./media/oracle-design/jumpbox.png)

    Počítač správce by měl nabízet jenom přístup s omezením IP adres jenom pro JumpBox. JumpBox by měl mít přístup k aplikaci a databázi.

- *Privátní síť* (podsítě): Doporučujeme, abyste měli aplikační službu a databázi v samostatných podsítích, takže lepší kontrolu můžete nastavit pomocí zásad NSG.


## <a name="additional-reading"></a>Další materiály ke čtení

- [Konfigurace Oracle ASM](configure-oracle-asm.md)
- [Konfigurace Oracle Data Guardu](configure-oracle-dataguard.md)
- [Konfigurace Oracle Zlaté brány](configure-oracle-golden-gate.md)
- [Zálohování a obnovení Oracle](./oracle-overview.md)

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)
- [Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](../../linux/cli-samples.md)
