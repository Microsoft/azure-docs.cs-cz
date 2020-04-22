---
title: Návrh a implementace databáze Oracle v Azure | Dokumenty společnosti Microsoft
description: Navrhujte a implementujte databázi Oracle ve vašem prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: ad446180b3bd864c5b6df808e6e4efac7d6c1c65
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687539"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Návrh a implementace databáze Oracle v Azure

## <a name="assumptions"></a>Předpoklady

- Plánujete migraci databáze Oracle z místního do Azure.
- Máte [sadu Diagnostics Pack](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) pro databázi Oracle, kterou chcete migrovat.
- Rozumíte různým metrikám v sestavách Oracle AWR.
- Máte základní znalosti výkonu aplikace a využití platformy.

## <a name="goals"></a>Cíle

- Zjistěte, jak optimalizovat nasazení Oracle v Azure.
- Prozkoumejte možnosti optimalizace výkonu pro databázi Oracle v prostředí Azure.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Rozdíly mezi místní a azure implementací 

Při migraci místních aplikací do Azure je třeba mít na paměti několik důležitých věcí. 

Jeden důležitý rozdíl je, že v implementaci Azure prostředky, jako jsou virtuální počítače, disky a virtuální sítě jsou sdíleny mezi ostatními klienty. Kromě toho prostředky mohou být omezeny na základě požadavků. Místo toho, aby se zaměřila na zamezení selhání (MTBF), Azure se více zaměřuje na přežití selhání (MTTR).

V následující tabulce jsou uvedeny některé rozdíly mezi místní implementací a implementací Azure databáze Oracle.

> 
> |  | **Místní implementace** | **Implementace Azure** |
> | --- | --- | --- |
> | **Sítě** |Lan/WAN  |SDN (softwarově definované sítě)|
> | **Skupina zabezpečení** |Nástroje pro omezení IP/portů |[Skupina zabezpečení sítě (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Odolnost** |MTBF (střední doba mezi poruchami) |MTTR (střední doba do zotavení)|
> | **Plánovaná údržba** |Opravy/upgrady|[Skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (opravy/upgrady spravované Azure) |
> | **Prostředek** |Vyhrazená  |Sdíleno s ostatními klienty|
> | **Oblasti** |Datová centra |[Párování oblastí](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Storage** |SAN/fyzické disky |[Spravované úložiště Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Měřítko** |Svislé měřítko |Horizontální škálování|


### <a name="requirements"></a>Požadavky

- Určete velikost databáze a rychlost růstu.
- Určete požadavky na viposlužby, které můžete odhadnout na základě sestav Oracle AWR nebo jiných nástrojů pro sledování sítě.

## <a name="configuration-options"></a>Možnosti konfigurace

Existují čtyři potenciální oblasti, které můžete naladit ke zlepšení výkonu v prostředí Azure:

- Velikost virtuálního počítače
- Propustnost sítě
- Typy a konfigurace disků
- Nastavení mezipaměti disku

### <a name="generate-an-awr-report"></a>Generovat sestavu AWR

Pokud máte existující databázi Oracle a plánujete migraci do Azure, máte několik možností. Pokud máte [sadu Diagnostics Pack](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) pro vaše instance Oracle, můžete spustit sestavu Oracle AWR a získat metriky (IOPS, Mbps, GiBs a tak dále). Pak zvolte virtuální počítač na základě metrik, které jste shromáždili. Nebo můžete kontaktovat svůj tým infrastruktury a získat podobné informace.

Můžete zvážit spuštění sestavy AWR během běžných úloh i úloh ve špičce, takže můžete porovnat. Na základě těchto sestav můžete velikost virtuálních počítačů na základě průměrné hozatížení nebo maximální zatížení.

Následuje příklad, jak generovat sestavu AWR (Vygenerujte sestavy AWR pomocí nástroje Oracle Enterprise Manager, pokud má aktuální instalace jeden):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Klíčové metriky

Níže jsou uvedeny metriky, které můžete získat z přehledu AWR:

- Celkový počet jader
- Rychlost hodin procesoru
- Celková paměť v GB
- Využití procesoru
- Maximální rychlost přenosu dat
- Rychlost vstupně-tovitých změn (čtení/zápis)
- Rychlost protokolování opakování (Mbps)
- Propustnost sítě
- Míra latence sítě (nízká/vysoká)
- Velikost databáze v GB
- Bajty přijaté prostřednictvím služby SQL*Net z/do klienta

### <a name="virtual-machine-size"></a>Velikost virtuálního počítače

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Odhad velikosti virtuálního počítače na základě využití procesoru, paměti a vstupně-v.I z sestavy AWR

Jedna věc, kterou můžete podívat na je prvních pět časované popředí události, které označují, kde jsou kritické body systému.

Například v následujícím diagramu je synchronizace souboru protokolu v horní části. Označuje počet čekání, které jsou požadovány před LGWR zapíše vyrovnávací paměť protokolu do souboru protokolu opakování. Tyto výsledky naznačují, že jsou vyžadovány lépe fungující úložiště nebo disky. Kromě toho diagram také zobrazuje počet procesoru (jader) a množství paměti.

![Snímek obrazovky se stavem AWR](./media/oracle-design/cpu_memory_info.png)

Následující diagram znázorňuje celkové vstupně-v.I/O čtení a zápisu. V době reportu bylo napsáno 59 GB a 247,3 GB.

![Snímek obrazovky se stavem AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Výběr virtuálního virtuálního mě

Na základě informací, které jste shromáždili ze sestavy AWR, je dalším krokem výběr virtuálního počítače podobné velikosti, který splňuje vaše požadavky. Seznam dostupných virtuálních počítačů najdete v článku [Optimalizované pro paměť](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Dolaďte velikost virtuálního počítače pomocí podobné řady virtuálních počítačů na základě ACU

Po výběru virtuálního virtuálního mísa, dávat pozor na ACU pro virtuální ho. Můžete zvolit jiný virtuální počítač na základě hodnoty ACU, která lépe vyhovuje vašim požadavkům. Další informace najdete v tématu [Výpočetní jednotka Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Snímek obrazovky se stránkou Jednotek ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Propustnost sítě

Následující diagram znázorňuje vztah mezi propustností a vipojem:

![Snímek obrazovky s propustností](./media/oracle-design/throughput.png)

Celková propustnost sítě se odhaduje na základě následujících informací:
- SQL*Čistý provoz
- Mb/s x počet serverů (odchozí datový proud, například Oracle Data Guard)
- Další faktory, jako je replikace aplikací

![Snímek obrazovky s propustností SÍTĚ SQL*Net](./media/oracle-design/sqlnet_info.png)

Na základě požadavků na šířku pásma sítě si můžete vybrat z různých typů bran. Patří mezi ně základní, VpnGw a Azure ExpressRoute. Další informace naleznete na [stránce s cenami brány VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h).

**Doporučení**

- Latence sítě je vyšší ve srovnání s místním nasazením. Snížení síťových zpátečních cest může výrazně zlepšit výkon.
- Chcete-li omezit zpáteční cesty, konsolidujte aplikace, které mají vysoké transakce nebo "chattované" aplikace na stejném virtuálním počítači.
- Používejte virtuální počítače se [zrychlenou sítí](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) pro lepší výkon sítě.
- U některých distrubutions Linux, zvažte povolení [TRIM / UNMAP podporu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support).
- Nainstalujte [aplikaci Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) do samostatného virtuálního počítače.
- Obrovské stránky nejsou povoleny na Linuxu ve výchozím nastavení. Zvažte povolení obrovské `use_large_pages = ONLY` stránky a nastavit na Oracle DB. To může pomoci zvýšit výkon. Více informací naleznete [zde](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Typy a konfigurace disků

- *Výchozí disky operačního systému*: Tyto typy disků nabízejí trvalá data a ukládání do mezipaměti. Jsou optimalizované pro přístup k os při spuštění a nejsou určeny pro transakční nebo datového skladu (analytické) úlohy.

- *Nespravované disky*: U těchto typů disků spravujete účty úložiště, které ukládají soubory virtuálního pevného disku (VHD), které odpovídají diskům virtuálního počítače. Soubory VHD se ukládají jako objekty BLOB stránky v účtech úložiště Azure.

- *Spravované disky*: Azure spravuje účty úložiště, které používáte pro vaše disky virtuálních počítačů. Zadejte typ disku (premium nebo standard) a velikost disku, který potřebujete. Azure vytvoří a spravuje disk za vás.

- *Disky úložiště Premium*: Tyto typy disků jsou nejvhodnější pro produkční úlohy. Úložiště Premium podporuje disky virtuálních počítačů, které lze připojit k virtuálním počítačům řady velikostí, jako jsou virtuální počítače řady DS, DSv2, GS a F. Disk premium je dodáván s různými velikostmi a můžete si vybrat mezi disky v rozsahu od 32 GB do 4 096 GB. Každá velikost disku má své vlastní specifikace výkonu. V závislosti na požadavcích aplikace můžete k virtuálnímu počítači připojit jeden nebo více disků.

Při vytváření nového spravovaného disku z portálu můžete zvolit **typ účtu** pro typ disku, který chcete použít. Nezapomeňte, že v rozevírací nabídce nejsou zobrazeny všechny dostupné disky. Po výběru konkrétní velikost i pro virtuální počítač se v nabídce zobrazí jenom dostupné skladové stony úložiště úrovně premium, které jsou založené na dané velikosti virtuálního počítače.

![Snímek obrazovky se stránkou spravovaného disku](./media/oracle-design/premium_disk01.png)

Po konfiguraci úložiště na virtuálním počítači, můžete chtít načíst testování disků před vytvořením databáze. Znalost vstupně-výstupní sazby z hlediska latence a propustnosti vám může pomoci určit, pokud virtuální chod y podporují očekávanou propustnost s cíli latence.

Existuje celá řada nástrojů pro testování zatížení aplikací, jako je například Oracle Orion, Sysbench a Fio.

Po nasazení databáze Oracle znovu spusťte zátěžový test. Spusťte pravidelné a špičkové úlohy a výsledky ukazují směrný plán vašeho prostředí.

Může být důležitější velikost úložiště na základě rychlosti viops spíše než velikost úložiště. Například pokud požadované IOPS je 5 000, ale potřebujete pouze 200 GB, můžete stále získat p30 třídy premium disk, i když je dodáván s více než 200 GB úložiště.

Sazbu VOPS lze získat ze zprávy AWR. Je určen protokol redo, fyzické čtení a zápisy sazby.

![Snímek obrazovky se stavem AWR](./media/oracle-design/awr_report.png)

Například velikost opakování je 12 200 000 bajtů za sekundu, což se rovná 11,63 mbp.
IOPS je 12,200,000 / 2,358 = 5,174.

Po dosažení jasného přehledu o požadavcích na vstupy a vstupně-va můžete zvolit kombinaci jednotek, které jsou nejvhodnější pro splnění těchto požadavků.

**Doporučení**

- V oblasti tabulky dat rozprostřete vstupně-rozložitelné pracovní vytížení na několik disků pomocí spravovaného úložiště nebo řešení Oracle ASM.
- Jak se velikost bloku vstupně-v.i. zvětšuje pro operace náročné na čtení a náročné na zápis, přidejte další datové disky.
- Zvětšete velikost bloku pro velké sekvenční procesy.
- Pomocí komprese dat můžete snížit vstupně-va (pro data i indexy).
- Samostatné protokoly opakování, systém a temps a vrátit ts na samostatných datových discích.
- Neumisňujte žádné soubory aplikací na výchozí disky operačního systému (/dev/sda). Tyto disky nejsou optimalizovány pro rychlé spouštění virtuálních počítačů a nemusí poskytovat dobrý výkon pro vaši aplikaci.
- Při použití virtuálních počítačů řady M v úložišti Premium povolte [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) na disku protokolů opakování.

### <a name="disk-cache-settings"></a>Nastavení mezipaměti disku

Existují tři možnosti ukládání hostitelů do mezipaměti:

- *Jen pro čtení*: Všechny požadavky jsou ukládány do mezipaměti pro budoucí čtení. Všechny zápisy jsou trvalé přímo do úložiště objektů blob Azure.

- *ReadWrite*: Jedná se o algoritmus "čtení napřed". Čtení a zápisy jsou uloženy do mezipaměti pro budoucí čtení. Zápisy bez zápisu jsou nejprve trvalé do místní mezipaměti. Poskytuje také nejnižší latenci disku pro lehké úlohy. Použití mezipaměti ReadWrite s aplikací, která nezpracovává trvalé požadované údaje může vést ke ztrátě dat, pokud dojde k chybě virtuálního virtuálního serveru.

- *Žádné* (zakázáno): Pomocí této možnosti můžete obejít mezipaměť. Všechna data se přenesou na disk a trvalé do Služby Azure Storage. Tato metoda poskytuje nejvyšší vstupně-v sazby pro vstupně-v.i. intenzivní úlohy. Je také třeba vzít v úvahu "transakční náklady".

**Doporučení**

Chcete-li maximalizovat propustnost, doporučujeme začít s **none** pro ukládání do mezipaměti hostitele. Pro úložiště Premium, mějte na paměti, že je nutné zakázat "bariéry" při připojení systému souborů s **ReadOnly** nebo **Žádné** možnosti. Aktualizujte soubor /etc/fstab pomocí UUID na disky.

![Snímek obrazovky se stránkou spravovaného disku](./media/oracle-design/premium_disk02.png)

- Pro disky operačního systému použijte výchozí ukládání do mezipaměti **pro čtení a zápis.**
- Pro SYSTEM, TEMP a UNDO použijte **žádné** pro ukládání do mezipaměti.
- Pro data použijte **žádné** pro ukládání do mezipaměti. Ale pokud je vaše databáze jen pro čtení nebo náročné na čtení, použijte ukládání do mezipaměti **jen pro čtení.**

Po uložení nastavení datového disku nelze změnit nastavení mezipaměti hostitele, pokud jednotku neodpojíte na úrovni operačního systému a nepřipojíte ji znovu po provedené změně.

## <a name="security"></a>Zabezpečení

Po nastavení a konfiguraci prostředí Azure je dalším krokem zabezpečení vaší sítě. Zde je několik doporučení:

- *Zásady skupiny nsg*: Skupinu síťových sítí lze definovat pomocí podsítě nebo síťové sítě. Řízení přístupu na úrovni podsítě je jednodušší, a to jak pro zabezpečení, tak pro směrování vynucovacích kódů pro věci, jako jsou brány firewall aplikací.

- *Jumpbox*: Pro bezpečnější přístup by se správci neměli přímo připojovat k aplikační službě nebo databázi. Jumpbox se používá jako médium mezi počítačem správce a prostředky Azure.
![Snímek obrazovky stránky topologie Jumpboxu](./media/oracle-design/jumpbox.png)

    Počítač správce by měl nabízet pouze přístup k rozhraní jumpbox s omezeným protokolem IP. Jumpbox by měl mít přístup k aplikaci a databázi.

- *Privátní síť* (podsítě): Doporučujeme, abyste měli aplikační službu a databázi v samostatných podsítích, takže lepší řízení lze nastavit zásadami skupiny zabezpečení sítě.


## <a name="additional-reading"></a>Dodatečné čtení

- [Konfigurace Oracle ASM](configure-oracle-asm.md)
- [Konfigurace Oracle Data Guardu](configure-oracle-dataguard.md)
- [Konfigurace služby Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Zálohování a obnovení společnosti Oracle](oracle-backup-recovery.md)

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření vysoce dostupných virtuálních měn](../../linux/create-cli-complete.md)
- [Prozkoumejte ukázky azure cli nasazení virtuálních montovek virtuálních montovek](../../linux/cli-samples.md)
