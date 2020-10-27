---
title: Průvodce nastavením velikosti clusteru HDInsight Interactive Query (LLAP)
description: Průvodce nastavením velikosti LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 57eba9cb6a9350ed66ae132e6a021098da2a4810
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533049"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Průvodce nastavením velikosti clusterů Azure HDInsight Interactive Query (LLAPa registru)

Tento dokument popisuje určení velikosti clusteru HDInsight Interactive Query (cluster LLAP) pro typickou úlohu pro dosažení přijatelného výkonu. Všimněte si, že doporučení uvedená v tomto dokumentu jsou obecná pravidla a konkrétní úlohy můžou potřebovat konkrétní ladění.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Výchozí typy virtuálních počítačů Azure pro cluster HDInsight Interactive Query (LLAP)**

| Typ uzlu      | Instance | Velikost     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB RAM, 400 GB SSD   |
| Zaměstnanec   | **D14 v2**        | **16 vCPU, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB RAM, 40 GB SSD       |

**_Poznámka: všechny doporučené konfigurace jsou založené na D14 v2 typu pracovní uzel_* _  

### <a name="_configuration"></a>*Konfigurace _:**    
| Konfigurační klíč      | Doporučená hodnota  | Popis |
| :---        |    :----:   | :---     |
| příz. NodeManager. Resource. paměť-MB | 102400 (MB) | Celková velikost paměti (v MB) pro všechny kontejnery PŘÍZe na uzlu | 
| příze. Scheduler. maximum – přidělení-MB | 102400 (MB) | Maximální přidělení pro každou žádost kontejneru v RM v MB. Požadavky na paměť vyšší než tato hodnota se projeví |
| příze. Scheduler. maximum-přidělení – virtuální jádra | 12 |Maximální počet jader procesoru pro každou žádost kontejneru na Správce prostředků. Žádosti, které jsou vyšší než tato hodnota, se neprojeví. |
| příze. NodeManager. Resource. CPU-virtuální jádra | 12 | Počet jader procesoru na NodeManager, které se dají přidělit pro kontejnery. |
| příze. Scheduler. Capacity. root. llap. Capacity | 80 (%) | Přidělení kapacity PŘÍZe pro frontu llap  |
| TEZ. am. Resource. Memory. MB | 4096 (MB) | Velikost paměti v MB, kterou má tez AppMaster použít |
| podregistr. Server2. TEZ. Sessions. per. default. Queue | <number_of_worker_nodes> |Počet relací pro každou frontu s názvem v podregistru. Server2. TEZ. default. Queues. Toto číslo odpovídá počtu koordinátorů dotazů (tez AMs). |
| podregistr. TEZ. Container. Size | 4096 (MB) | Zadaná velikost kontejneru tez v MB |
| hive.llap.daemon.num.executors | 12 | Počet prováděcích modulů na démona LLAP | 
| podregistr. llap. IO. nevláken. Size | 12 | Velikost fondu vláken pro prováděcí moduly |
| podregistr. llap. démon. příze. Container. MB | 77824 (MB) | Celková paměť v MB používaná jednotlivými démony LLAP (paměť na démona)
| podregistr. llap. IO. Memory. Size | 235520 (MB) | Velikost mezipaměti v MB na LLAP démon, poskytnutá mezipaměť SSD, je povolená. |
| podregistr. auto. Convert. Join. noconditionaltask. Size | 2048 (MB) | velikost paměti v MB pro spojení mapování |

### <a name="llap-daemon-size-estimations"></a>**Odhad velikosti LLAP démona:**  

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. určení celkového přidělení paměti PŘÍZ pro všechny kontejnery na uzlu**    
Konfigurace: * *_nitě. NodeManager. Resource. Memory-MB_* _  

Tato hodnota označuje maximální velikost paměti v MB, kterou lze použít kontejnery PŘÍZe na jednotlivých uzlech. Zadaná hodnota by měla být menší než celková velikost fyzické paměti v tomto uzlu.   
Celková paměť pro všechny kontejnery PŘÍZe na uzlu = [celková fyzická paměť] – [paměť pro operační systém + další služby]  
Nastavte tuto hodnotu na ~ 90% dostupné velikosti paměti RAM.  
V případě D14 v2 je doporučená hodnota _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. určení maximálního množství paměti na žádost o PŘÍZi kontejneru**  
Konfigurace: * *_příz. Scheduler. maximum-Allocation-MB_* _

Tato hodnota označuje maximální přidělení pro každou žádost kontejneru v Správce prostředků v MB. Požadavky na paměť vyšší, než je zadaná hodnota, se projeví. Správce prostředků může poskytnout paměť kontejnerům v přírůstcích po _yarn. Scheduler. minimum-Allocation-MB * a nemůže překročit velikost určenou parametrem *nitě. Scheduler. maximum-Allocation-MB* . Zadaná hodnota by neměla být větší než celková velikost dané paměti pro všechny kontejnery v uzlu určeném pomocí *příz. NodeManager. Resource. Memory-MB* .    
V případě pracovních uzlů D14 v2 je doporučená hodnota **102400 MB** .

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. určení maximálního počtu virtuální jádra na požadavek kontejneru na PŘÍZi**  
Konfigurace: * *_příz. Scheduler. maximum-Allocation-virtuální jádra_* _  

Tato hodnota označuje maximální počet jader virtuálního procesoru pro každou žádost kontejneru na Správce prostředků. Požaduje se vyšší počet virtuální jádra, než tato hodnota se projeví. Jedná se o globální vlastnost plánovače PŘÍZe. V případě kontejneru démona LLAP lze tuto hodnotu nastavit na 75% z celkového počtu dostupných virtuální jádra. Zbývající 25% by mělo být rezervované pro NodeManager, datanode a další služby spuštěné na pracovních uzlech.  
Pro virtuální počítače D14 v2 je 16 virtuální jádra a 75% z celkového počtu 16 virtuální jádra může použít kontejner procesu LLAP.  
V případě D14 v2 je doporučená hodnota _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. počet souběžných dotazů**  
Konfigurace: * *_podregistr. Server2. TEZ. Sessions. per. default. Queue_* _

Tato hodnota konfigurace určuje počet tez relací, které lze spustit paralelně. Tyto relace tez se spustí pro každou frontu určenou v podregistru. Server2. TEZ. default. Queues. Odpovídá počtu tez AMs (koordinátory dotazů). Doporučuje se to být stejné jako počet pracovních uzlů. Počet tez AMs může být vyšší než počet uzlů LLAP démon. Primární zodpovědností tez je koordinovat provádění dotazů a přiřadit fragmenty plánu dotazů odpovídajícím démonům LLAP pro provedení. Tuto hodnotu nechte jako násobek několika uzlů LLAP démona, abyste dosáhli vyšší propustnosti.  

Výchozí cluster HDInsight má čtyři procesy LLAP démony spuštěné na čtyřech pracovních uzlech, takže doporučená hodnota je _ * 4 * *.  

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. tez kontejner a velikost hlavní aplikace tez**    
Konfigurace: * *_TEZ. am. Resource. Memory. MB, podregistr. TEZ. Container. Size_* _  

_tez. am. Resource. Memory. MB * – definuje velikost hlavní databáze aplikace TEZ.  
Doporučená hodnota je **4096 MB** .
   
*podregistr. TEZ. Container. Size* – definuje množství paměti zadané pro kontejner TEZ. Tato hodnota musí být nastavena mezi minimální velikostí kontejneru PŘÍZe ( *příz. Scheduler. minimum-Allocation-MB* ) a maximální velikostí kontejneru nitě ( *příz. Scheduler. Max-Allocation-MB* ). Vykonavatel LLAP démon tuto hodnotu používá pro omezení využití paměti na vykonavatele.  
Doporučená hodnota je **4096 MB** .  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. přidělení kapacity fronty LLAP**   
Konfigurace: * *_příze. Scheduler. Capacity. root. llap. Capacity_* _  

Tato hodnota označuje procento kapacity přidělené frontě llap. Přidělení kapacity může mít různé hodnoty pro různé úlohy v závislosti na tom, jak jsou nakonfigurované fronty PŘÍZe. Pokud vaše úloha je operací jen pro čtení, pak ji nastavte tak vysoké, jako je 90% kapacity by měla fungovat. Pokud je ale vaše zatížení kombinací operací aktualizovat/odstranit/sloučit pomocí spravovaných tabulek, doporučujeme poskytnout 80% kapacitu llap fronty. Zbývajících 20% kapacity můžou používat jiné úkoly, jako je například komprimace atd. k přidělování kontejnerů z výchozí fronty. Díky úlohám ve výchozí frontě nebude možné prostředky PŘÍZe nezbavit.    
Pro pracovní uzly D14v2 je doporučená hodnota pro llap Queue _ * 80 * *.   
(Pro úlohy jen pro čtení se dá zvýšit až 90.)  

#### <a name="7-llap-daemon-container-size"></a>**7. velikost kontejneru démona LLAP**    
Konfigurace: * *_podregistr. llap. démon. příze. Container. MB_* _  
   
Démon LLAP je spuštěn jako kontejner PŘÍZe v každém pracovním uzlu. Celková velikost paměti pro kontejner démon LLAP závisí na následujících faktorech:    
_ Konfigurace velikosti kontejneru PŘÍZe (příz. Scheduler. minima alokace-MB, příze. Scheduler. maximum-Allocation-MB, příze. NodeManager. Resource. Memory-MB)
*  Počet tez AMs na uzlu
*  Celková paměť konfigurovaná pro všechny kontejnery na kapacitě uzlu a frontě LLAP  

Paměť, kterou vyžadují hlavní aplikační servery tez (tez), se dají vypočítat takto.  
Pro interaktivní cluster HDInsight je ve výchozím nastavení jeden tez uzel v jednom pracovním uzlu, který funguje jako koordinátor dotazů. Počet tez AMs se dá nakonfigurovat na základě počtu souběžných dotazů, které se mají zpracovat.
Doporučuje se mít 4 GB paměti na tez dop.

Tez paměti na uzel = [ceil – (počet tez AMs/počet uzlů procesu démon pro LLAP)] x [velikost kontejneru pro tez]  
Pro D14 v2 má výchozí konfigurace čtyři tez AMs a čtyři uzly LLAP démon.  
Tez paměti na uzel = (ceil – (4/4) × 4 GB) = 4 GB

Celková paměť dostupná pro frontu LLAP na jeden pracovní uzel se dá vypočítat takto: Tato hodnota závisí na celkovém množství paměti dostupném pro všechny kontejnery PŘÍZe na uzlu ( *příze. NodeManager. Resource. Memory-MB* ) a na procentuální hodnotě kapacity nakonfigurované pro frontu LLAP ( *příz. Scheduler. Capacity. root. LLAP. Capacity* ).  
Celková paměť pro frontu LLAP v pracovním uzlu = celková paměť dostupná pro všechny kontejnery PŘÍZe v procentech kapacity uzlu x v procentech pro frontu LLAP.  
V případě D14 v2 je tato hodnota [100 GB × 0,80] = 80 GB.

Velikost kontejneru démona LLAP se vypočítá takto:

**Velikost kontejneru démona LLAP = [Celková dostupná paměť pro frontu LLAP] – [tez paměť na uzel]**  
    
Pro pracovní uzel D14 v2, HDI 4,0 – doporučená hodnota je (80 GB-4 GB)) = **76 GB**   
(Pro HDI 3,6 je doporučená hodnota **74 GB** , protože byste měli vyhradit další ~ 2 GB pro posuvník.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. určení počtu prováděcích modulů na démona LLAP**  
Konfigurace: * *_hive.llap.daemon.num.executors_* _, _*_podregistr. llap. IO. nevlákenná. Size_*_

_*_hive.llap.daemon.num.executors_*_ :   
Tato konfigurace řídí počet prováděcích modulů, které mohou paralelně provádět úlohy na LLAP démon. Tato hodnota závisí na počtu virtuální jádra, velikosti paměti dané na vykonavateli a celkovém množství paměti, které je k dispozici pro démona LLAP. Obvykle by tato hodnota měla být co nejblíže k počtu virtuální jádra.
Na virtuálních počítačích s D14 v2 je 16 virtuální jádra. Nicméně ne všechny virtuální jádra mohou být provedeny, protože jiné služby, jako je NodeManager, datanode, metrika a monitor atd. také potřebují část dostupného virtuální jádra. 

Pokud potřebujete upravit počet prováděcích modulů, doporučujeme zvážit 4 GB paměti na jeden prováděcí modul, jak je určeno _hive. TEZ. Container. Size * a zajistit, aby celková paměť potřebná pro všechny prováděcí moduly nepřekročila celkovou paměť dostupnou pro kontejner démona LLAP.  
Tato hodnota se dá nakonfigurovat až na 75% z celkového počtu virtuální jádra dostupných v tomto uzlu.  
V případě D14 v2 je doporučená hodnota (. 75 × 16) = **12**

**_podregistr. llap. IO. Apartment. Size_*_: Tato hodnota určuje velikost fondu vláken pro vykonavatele. Vzhledem k tomu, že vykonavatelé jsou pevně určení, bude stejný jako počet prováděcích modulů na démon LLAP. V případě D14 v2 je doporučená hodnota _* 12** .

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. určení velikosti mezipaměti démona LLAP**  
Konfigurace: * *_podregistr. llap. IO. Memory. Size_* _

Paměť kontejneru démona LLAP se skládá z následujících součástí: _ Hlavní místnost
*  Paměť haldy využívané prováděcími moduly (XMX)
*  Mezipaměť v paměti na démon (jeho velikost paměti mimo haldu, která se nedá použít, když je povolená mezipaměť SSD)
*  Velikost metadat mezipaměti v paměti (platí jenom v případě, že je povolená mezipaměť SSD)

**Velikost rezervy** : Tato velikost indikuje část paměti z jiné haldy, která se používá pro režie virtuálních počítačů Java (metaspace, Thread Stack, datové struktury GC atd.). Obecně platí, že tato režie je přibližně 6% velikosti haldy (XMX). Aby bylo možné na bezpečnější straně být tato hodnota vypočítaná jako 6% z celkové velikosti paměti démona LLAP.  
V případě D14 v2 je doporučená hodnota ceil – (76 GB × 0,06) ~ = **5 GB** .  

**Velikost haldy (XMX)** : je množství paměti haldy, která je k dispozici pro všechny prováděcí moduly.
Celková velikost haldy = počet prováděcích modulů × 4 GB  
Pro D14 v2 je tato hodnota 12 × 4 GB = **48 GB** .  

Pokud je mezipaměť SSD zakázaná, mezipaměť v paměti má velikost paměti, která je ponechána po pořízení velikosti a velikosti haldy z kontejneru LLAP démona.

Výpočet velikosti mezipaměti se liší, pokud je povolená mezipaměť SSD.
Nastavení *podregistru. llap. IO. Alokátor. mmap* = true povolí ukládání do mezipaměti SSD.
Pokud je povolená mezipaměť SSD, použije se část paměti k uložení metadat pro mezipaměť SSD. Metadata jsou uložená v paměti a očekává se, že bude mít velikost mezipaměti SSD 10%.   
Velikost metadat v paměti SSD = [velikost kontejneru démona LLAP] – [hlavní místnost + velikost haldy]  
V případě D14 v2 s HDI 4,0 je velikost metadat v paměti SSD = [76 GB] – [5 GB + 48 GB] = **23 GB**  
V případě D14 v2 s HDI 3,6 je velikost metadat v paměti SSD = [76 GB] – [5 GB + 48 GB + 2 GB posuvník] = **21 GB**

Vzhledem k velikosti dostupné paměti pro ukládání metadat mezipaměti SSD můžeme vypočítat velikost mezipaměti SSD, kterou je možné podporovat.  
Velikost metadat v paměti pro mezipaměť SSD = 10% velikosti mezipaměti SSD       
Velikost mezipaměti SSD = velikost metadat v paměti pro mezipaměť SSD x 10  

Pro D14 v2 a HDI 4,0 se doporučuje velikost mezipaměti SSD = 23 GB × 10 = **230 GB**  
Pro D14 v2 a HDI 3,6 se doporučuje velikost mezipaměti SSD = 21 GB × 10 = **210 GB** .

#### <a name="10-adjusting-map-join-memory"></a>**10. Úprava paměti připojení mapy**   
Konfigurace: * *_podregistr. auto. Convert. Join. noconditionaltask. Size_* _

Ujistěte se, že máte _hive. auto. Convert. Join. noconditionaltask * Enabled, aby se tento parametr projevil.
Tato konfigurace umožňuje uživateli určit velikost tabulek, které mohou být do paměti přizpůsobeny, aby se připojily k mapě. Je-li součet velikosti n-1 tabulek nebo oddílů pro n-Way spojení menší než konfigurovaná hodnota, bude zvolena možnost spojení map. Velikost paměti prováděcího modulu LLAP by měla být použita k výpočtu prahové hodnoty autoConvert pro připojení k mapě.
U každého prováděcího modulu se předpokládá, že má velikost haldy 4 GB, ale ne všechny budou k dispozici pro připojení k mapě. Některá paměť haldy se použije pro řazení vyrovnávacích pamětí, náhodné vyrovnávací paměti, zatřiďovací tabulky atd. jiné operace také. Proto můžete pro připojení k mapě dát 50% velikost paměti haldy o velikosti 4 GB.  
Poznámka: Tato hodnota může vyžadovat úpravy, které jsou vhodné pro vaše zatížení. Nastavení příliš nízké hodnoty nemusí používat funkci autoConvert. A jeho nastavení je příliš vysoké, může způsobit výjimky z paměti nebo pozastavení GC, které mohou vést k negativnímu výkonu.  
V případě D14 v2 se 4 GB paměti na vykonavatele doporučujeme nastavit tuto hodnotu na **2048 MB** .


#### <a name="next-steps"></a>**Další kroky**
Pokud nastavení těchto hodnot nevyřešilo váš problém, navštivte jednu z následujících možností...

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Další odkazy:**
  * [Konfigurace dalších vlastností LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Konfigurace velikosti haldy serveru s podregistr](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Mapování velikosti paměti spojení pro LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Vlastnosti prováděcího modulu tez](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [LLAP hloubkové podrobně pro podregistr](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)