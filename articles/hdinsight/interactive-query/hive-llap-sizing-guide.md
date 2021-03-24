---
title: Průvodce nastavením velikosti clusteru HDInsight Interactive Query (LLAP)
description: Průvodce nastavením velikosti LLAP
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: ca3ba61de13e0e451b43dc9c8ea40db33fed859a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869663"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Průvodce nastavením velikosti clusterů Azure HDInsight Interactive Query (LLAPa registru)

Tento dokument popisuje určení velikosti clusteru HDInsight Interactive Query (cluster LLAP) pro typickou úlohu pro dosažení přijatelného výkonu. Všimněte si, že doporučení uvedená v tomto dokumentu jsou obecná pravidla a konkrétní úlohy můžou potřebovat konkrétní ladění.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Výchozí typy virtuálních počítačů Azure pro cluster HDInsight Interactive Query (LLAP)**

| Typ uzlu      | Instance | Velikost     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB RAM, 400 GB SSD   |
| Zaměstnanec   | **D14 v2**        | **16 vCPU, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB RAM, 40 GB SSD       |

***Poznámka: všechny doporučené konfigurace jsou založené na pracovním uzlu typ D14 v2.***  

### <a name="configuration"></a>**Rozšířeného**    
| Konfigurační klíč      | Doporučená hodnota  | Popis |
| :---        |    :----:   | :---     |
| příz. NodeManager. Resource. paměť-MB | 102400 (MB) | Celková velikost paměti (v MB) pro všechny kontejnery PŘÍZe na uzlu | 
| příze. Scheduler. maximum – přidělení-MB | 102400 (MB) | Maximální přidělení pro každou žádost kontejneru v RM v MB. Požadavky na paměť vyšší než tato hodnota se projeví |
| příze. Scheduler. maximum-přidělení – virtuální jádra | 12 |Maximální počet jader procesoru pro každou žádost kontejneru na Správce prostředků. Žádosti, které jsou vyšší než tato hodnota, se neprojeví. |
| příze. NodeManager. Resource. CPU-virtuální jádra | 12 | Počet jader procesoru na NodeManager, které se dají přidělit pro kontejnery. |
| příze. Scheduler. Capacity. root. llap. Capacity | 85 (%) | Přidělení kapacity PŘÍZe pro frontu llap  |
| TEZ. am. Resource. Memory. MB | 4096 (MB) | Velikost paměti v MB, kterou má tez AppMaster použít |
| podregistr. Server2. TEZ. Sessions. per. default. Queue | <number_of_worker_nodes> |Počet relací pro každou frontu s názvem v podregistru. Server2. TEZ. default. Queues. Toto číslo odpovídá počtu koordinátorů dotazů (tez AMs). |
| podregistr. TEZ. Container. Size | 4096 (MB) | Zadaná velikost kontejneru tez v MB |
| hive.llap.daemon.num.executors | 19 | Počet prováděcích modulů na démona LLAP | 
| podregistr. llap. IO. nevláken. Size | 19 | Velikost fondu vláken pro prováděcí moduly |
| podregistr. llap. démon. příze. Container. MB | 81920 (MB) | Celková paměť v MB používaná jednotlivými démony LLAP (paměť na démona)
| podregistr. llap. IO. Memory. Size | 242688 (MB) | Velikost mezipaměti v MB na LLAP démon, poskytnutá mezipaměť SSD, je povolená. |
| podregistr. auto. Convert. Join. noconditionaltask. Size | 2048 (MB) | velikost paměti v MB pro spojení mapování |

### <a name="llap-architecturecomponents"></a>**LLAP architektury/komponenty:**  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png " alt-text="Architektura LLAP/součásti" border="true":::

### <a name="llap-daemon-size-estimations"></a>**Odhad velikosti LLAP démona:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. určení celkového přidělení paměti PŘÍZ pro všechny kontejnery na uzlu**    
Konfigurace: ***příze. NodeManager. Resource. Memory-MB***  

Tato hodnota označuje maximální velikost paměti v MB, kterou lze použít kontejnery PŘÍZe na jednotlivých uzlech. Zadaná hodnota by měla být menší než celková velikost fyzické paměti v tomto uzlu.   
Celková paměť pro všechny kontejnery PŘÍZe na uzlu = (celková fyzická paměť – paměť pro operační systém + další služby)  
Nastavte tuto hodnotu na ~ 90% dostupné velikosti paměti RAM.  
V případě D14 v2 je doporučená hodnota **102400 MB**. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. určení maximálního množství paměti na žádost o PŘÍZi kontejneru**  
Konfigurace: ***příze. Scheduler. maximum-Allocation-MB***

Tato hodnota označuje maximální přidělení pro každou žádost kontejneru v Správce prostředků v MB. Požadavky na paměť vyšší, než je zadaná hodnota, se projeví. Správce prostředků může poskytnout paměť kontejnerům v přírůstcích po *přízi. Scheduler. minimum-Allocation-MB* a nemůže překročit velikost určenou parametrem *nitě. Scheduler. Max-Allocation-MB*. Zadaná hodnota by neměla být větší než celková velikost dané paměti pro všechny kontejnery v uzlu určeném pomocí *příz. NodeManager. Resource. Memory-MB*.    
V případě pracovních uzlů D14 v2 je doporučená hodnota **102400 MB** .

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. určení maximálního počtu virtuální jádra na požadavek kontejneru na PŘÍZi**  
Konfigurace: ***příze. Scheduler. maximum-Allocation-virtuální jádra***  

Tato hodnota označuje maximální počet jader virtuálního procesoru pro každou žádost kontejneru na Správce prostředků. Požaduje se vyšší počet virtuální jádra, než tato hodnota se projeví. Jedná se o globální vlastnost plánovače PŘÍZe. V případě kontejneru démona LLAP lze tuto hodnotu nastavit na 75% z celkového počtu dostupných virtuální jádra. Zbývající 25% by mělo být rezervované pro NodeManager, datanode a další služby spuštěné na pracovních uzlech.  
Pro virtuální počítače D14 v2 je 16 virtuální jádra a 75% z celkového počtu 16 virtuální jádra může použít kontejner procesu LLAP.  
V případě D14 v2 je doporučená hodnota **12**.  

#### <a name="4-number-of-concurrent-queries"></a>**4. počet souběžných dotazů**  
Konfigurace: ***podregistr. Server2. TEZ. Sessions. per. default. Queue***

Tato hodnota konfigurace určuje počet tez relací, které lze spustit paralelně. Tyto relace tez se spustí pro každou frontu určenou v podregistru. Server2. TEZ. default. Queues. Odpovídá počtu tez AMs (koordinátory dotazů). Doporučuje se to být stejné jako počet pracovních uzlů. Počet tez AMs může být vyšší než počet uzlů LLAP démon. Primární zodpovědností tez je koordinovat provádění dotazů a přiřadit fragmenty plánu dotazů odpovídajícím démonům LLAP pro provedení. Tuto hodnotu nechte jako násobek několika uzlů LLAP démona, abyste dosáhli vyšší propustnosti.  

Výchozí cluster HDInsight má čtyři procesy démony LLAP spuštěné na čtyřech pracovních uzlech, proto je doporučená hodnota **4**.  

**Posuvník uživatelského rozhraní Ambari pro proměnnou konfigurace podregistru `hive.server2.tez.sessions.per.default.queue` :**

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png " alt-text="Maximální počet souběžných dotazů LLAP" border="true":::

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. tez kontejner a velikost hlavní aplikace tez**    
Konfigurace: ***TEZ. am. Resource. Memory. MB, podregistr. TEZ. Container. Size***  

*TEZ. am. Resource. Memory. MB* – definuje velikost hlavní databáze aplikace TEZ.  
Doporučená hodnota je **4096 MB**.
   
*podregistr. TEZ. Container. Size* – definuje množství paměti zadané pro kontejner TEZ. Tato hodnota musí být nastavena mezi minimální velikostí kontejneru PŘÍZe (*příz. Scheduler. minimum-Allocation-MB*) a maximální velikostí kontejneru nitě (*příz. Scheduler. Max-Allocation-MB*). Vykonavatel LLAP démon tuto hodnotu používá pro omezení využití paměti na vykonavatele.  
Doporučená hodnota je **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. přidělení kapacity fronty LLAP**   
Konfigurace: ***příze. Scheduler. Capacity. root. llap. Capacity***  

Tato hodnota označuje procento kapacity přidělené frontě llap. Přidělení kapacity může mít různé hodnoty pro různé úlohy v závislosti na tom, jak jsou nakonfigurované fronty PŘÍZe. Pokud vaše úloha je operací jen pro čtení, pak ji nastavte tak vysoké, jako je 90% kapacity by měla fungovat. Pokud je ale vaše zatížení kombinací operací aktualizovat/odstranit/sloučit pomocí spravovaných tabulek, doporučujeme poskytnout 85% kapacitu llap fronty. Zbývajících 15% kapacity lze použít jinými úkoly, jako je například komprimace atd. pro přidělení kontejnerů z výchozí fronty. Díky úlohám ve výchozí frontě nebude možné prostředky PŘÍZe nezbavit.    

Pro pracovní uzly D14v2 je doporučená hodnota pro llap Queue **85**.     
(Pro úlohy jen pro čtení se dá zvýšit až 90.)  

#### <a name="7-llap-daemon-container-size"></a>**7. velikost kontejneru démona LLAP**    
Konfigurace: ***podregistr. llap. démon. příze. Container. MB***  
   
Démon LLAP je spuštěn jako kontejner PŘÍZe v každém pracovním uzlu. Celková velikost paměti pro kontejner démon LLAP závisí na následujících faktorech:    
*  Konfigurace velikosti kontejneru PŘÍZe (příz. Scheduler. minimum-Allocation-MB, příz. Scheduler. maximum-Allocation-MB, příze. NodeManager. Resource. Memory-MB)
*  Počet tez AMs na uzlu
*  Celková paměť konfigurovaná pro všechny kontejnery na kapacitě uzlu a frontě LLAP  

Paměť, kterou vyžadují hlavní aplikační servery tez (tez), se dají vypočítat takto.  
Tez slouží jako koordinátor dotazů a počet tez AMs by měl být nakonfigurován na základě počtu souběžných dotazů, které mají být obsluhovány. Teoreticky můžeme vzít v úvahu jeden tez uzel v jednom pracovním uzlu. Je však možné, že se v pracovním uzlu zobrazí více než jedna TEZ. Pro účely výpočtu předpokládáme jednotnou distribuci tez AMs ve všech uzlech LLAP démon/pracovních uzlech.
Doporučuje se mít 4 GB paměti na tez dop.  

Počet tez AMS = hodnota zadaná ***podregistrem konfigurace podregistru. Server2. TEZ. Sessions. per. default. Queue***.  
Počet uzlů LLAP démona = určených proměnnou ENV ***num_llap_nodes_for_llap_daemons*** v uživatelském rozhraní Ambari  
Velikost kontejneru tez AM = hodnota zadaná tez config ***TEZ. am. Resource. Memory. MB***.  

Tez paměti na uzel = **(** ceil – **(** počet tez AMs pro **/** uzly procesu démon LLAP **)** **×** tez velikost kontejneru **)**  
Pro D14 v2 má výchozí konfigurace čtyři tez AMs a čtyři uzly LLAP démon.  
Tez paměti na uzel = (ceil – (4/4) × 4 GB) = 4 GB

Celková paměť dostupná pro frontu LLAP na jeden pracovní uzel se dá vypočítat takto:  
Tato hodnota závisí na celkovém množství paměti, které je k dispozici pro všechny kontejnery PŘÍZe na uzlu (*příze. NodeManager. Resource. paměť-MB*) a na procentu kapacity nakonfigurované pro frontu llap (*příze. Scheduler. Capacity. root. llap. Capacity*).  
Celková paměť pro frontu LLAP v pracovním uzlu = celková paměť dostupná pro všechny kontejnery PŘÍZe v procentech kapacity uzlu x v procentech pro frontu LLAP.  
V případě D14 v2 je tato hodnota (100 GB × 0,85) = 85 GB.

Velikost kontejneru démona LLAP se vypočítá takto:

**Velikost kontejneru démona LLAP = (celková paměť pro frontu LLAP na workernode) – (tez paměti na uzel) – (velikost kontejneru hlavního serveru služby)**  
V clusteru je v jednom z pracovních uzlů pouze jeden hlavní server služby (hlavní server aplikace pro službu LLAP). Pro účely výpočtu považujeme jeden hlavní uzel služby na pracovní uzel.  
Pro pracovní uzel D14 v2, HDI 4,0 – doporučená hodnota je (85 GB-4 GB-1 GB)) = **80 GB**   
(Pro HDI 3,6 je doporučená hodnota **79 GB** , protože byste měli vyhradit další ~ 2 GB pro posuvník.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. určení počtu prováděcích modulů na démona LLAP**  
Konfigurace: ***hive.llap.daemon.num.executors** _, _ *_podregistr. llap. IO. nevlákenná velikost_**

***hive.llap.daemon.num.executors***:   
Tato konfigurace řídí počet prováděcích modulů, které mohou paralelně provádět úlohy na LLAP démon. Tato hodnota závisí na počtu virtuální jádra, velikosti paměti používané na vykonavateli a celkovém množství dostupné paměti pro kontejner démona LLAP.    Počet prováděcích modulů se dá přeodebírat 120% dostupné virtuální jádra na pracovní uzel. Měla by však být upravena, pokud nesplňuje požadavky na paměť na základě paměti potřebné na vykonavatele a velikosti kontejneru démona LLAP.

Každý prováděcí modul je ekvivalentní tez kontejneru a může spotřebovávat 4GB (velikost kontejneru tez) paměti. Všechny prováděcí moduly v procesu démon LLAP sdílejí stejnou paměť haldy. S předpokladem, že ne všechny vykonavatelé spouštějí operace náročné na paměť ve stejnou dobu, můžete zvážit 75% velikosti kontejneru tez (4 GB) na jeden prováděcí modul. Tímto způsobem můžete zvýšit počet prováděcích modulů tím, že každému prováděcímu modulu udělíte méně paměti (například 3 GB) pro zvýšení paralelismu. Doporučuje se ale toto nastavení optimalizovat pro vaše cílové úlohy.

Na virtuálních počítačích s D14 v2 je 16 virtuální jádra.
Pro D14 v2 je doporučená hodnota pro počet prováděcích modulů (16 virtuální jádra x 120%) ~ = **19** na každém pracovním uzlu, který zvažuje povolenou na vykonavatele.

***podregistr. llap. IO. nevláken. Size***:   
Tato hodnota určuje velikost fondu vláken pro vykonavatele. Vzhledem k tomu, že vykonavatelé jsou pevně určení, bude stejný jako počet prováděcích modulů na démon LLAP.    
V případě D14 v2 je doporučená hodnota **19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. určení velikosti mezipaměti démona LLAP**  
Konfigurace: ***podregistr. llap. IO. Memory. Size***

Paměť kontejneru démona LLAP se skládá z následujících součástí:
*  Hlavní místnost
*  Paměť haldy využívané prováděcími moduly (XMX)
*  Mezipaměť v paměti na démon (jeho velikost paměti mimo haldu, která se nedá použít, když je povolená mezipaměť SSD)
*  Velikost metadat mezipaměti v paměti (platí jenom v případě, že je povolená mezipaměť SSD)

**Velikost rezervy**: Tato velikost indikuje část paměti z jiné haldy, která se používá pro režie virtuálních počítačů Java (metaspace, Thread Stack, datové struktury GC atd.). Obecně platí, že tato režie je přibližně 6% velikosti haldy (XMX). Aby bylo možné na bezpečnější straně být tato hodnota vypočítaná jako 6% z celkové velikosti paměti démona LLAP.  
V případě D14 v2 je doporučená hodnota ceil – (80 GB × 0,06) ~ = **4 GB**.  

**Velikost haldy (XMX)**: je množství paměti haldy, která je k dispozici pro všechny prováděcí moduly.
Celková velikost haldy = počet prováděcích modulů × 3 GB  
Pro D14 v2 je tato hodnota 19 × 3 GB = **57 GB** .  

`Ambari environment variable for LLAP heap size:`

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png " alt-text="Velikost haldy LLAP" border="true":::

Pokud je mezipaměť SSD zakázaná, mezipaměť v paměti je množství paměti, které zbývá po pořízení velikosti a velikosti haldy z kontejneru LLAP démona.

Výpočet velikosti mezipaměti se liší, pokud je povolená mezipaměť SSD.
Nastavení *podregistru. llap. IO. Alokátor. mmap* = true povolí ukládání do mezipaměti SSD.
Pokud je povolená mezipaměť SSD, použije se část paměti k uložení metadat pro mezipaměť SSD. Metadata jsou uložená v paměti a očekává se, že bude ~ 8% velikosti mezipaměti SSD.   
Velikost metadat v paměti SSD = velikost kontejneru démona LLAP – (hlava místnosti + velikost haldy)  
V případě D14 v2 s HDI 4,0 je velikost metadat v paměti SSD = 80 GB – (4 GB + 57 GB) = **19 GB**  
V případě D14 v2 s HDI 3,6 je velikost metadat v paměti SSD = 79 GB – (4 GB + 57 GB) = **18 GB**

Vzhledem k velikosti dostupné paměti pro ukládání metadat mezipaměti SSD můžeme vypočítat velikost mezipaměti SSD, kterou je možné podporovat.  
Velikost metadat v paměti pro mezipaměť SSD = velikost kontejneru LLAP démona – (hlava místnosti + velikost haldy) = 19 GB     
Velikost mezipaměti SSD = velikost metadat v paměti pro mezipaměť SSD (19 GB)/0,08 (8 procent)  

Pro D14 v2 a HDI 4,0 se doporučuje velikost mezipaměti SSD = 19 GB/0,08 ~ = **237 GB** .  
Pro D14 v2 a HDI 3,6 se doporučuje velikost mezipaměti SSD = 18 GB/0,08 ~ = **225 GB** .

#### <a name="10-adjusting-map-join-memory"></a>**10. Úprava paměti připojení mapy**   
Konfigurace: ***podregistr. auto. Convert. Join. noconditionaltask. Size***

Ujistěte se, že máte *podregistr. auto. Convert. noconditionaltask* povolený, aby se tento parametr projevil.
Tato konfigurace Určuje prahovou hodnotu pro MapJoin výběr podle Optimalizátoru podregistru, který má za následek, že se má více místa pro tabulky hash v paměti, aby bylo možné použít větší počet převodů spojení mapování. V rámci povolenou na vykonavatele se tato velikost dá přehlásit povolenou, ale některá paměť haldy se dá také použít pro řazení vyrovnávacích pamětí, náhodné vyrovnávací paměti atd. ostatní operace.   
Takže pro D14 v2 se 3 GB paměti na vykonavatele doporučujeme nastavit tuto hodnotu na **2048 MB**.  

(Poznámka: Tato hodnota může vyžadovat úpravy, které jsou vhodné pro vaše zatížení. Nastavení příliš nízké hodnoty nemusí používat funkci autoConvert. A nastavení příliš vysokého může způsobit výjimky z paměti nebo pozastavení GC, které mohou vést k negativnímu výkonu.)  

#### <a name="11-number-of-llap-daemons"></a>**11. počet LLAP démonů**
Proměnné prostředí Ambari: ***num_llap_nodes, num_llap_nodes_for_llap_daemons***  

**num_llap_nodes** – určuje počet uzlů, které používá služba Llap podregistru, zahrnuje uzly se spuštěným llap démonem, hlavním serverem služby Llap a tez hlavní aplikační sadu (tez am).  

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png " alt-text="' Počet uzlů pro službu LLAP '" border="true":::  

**num_llap_nodes_for_llap_daemons** – zadaný počet uzlů, které se používají jenom pro procesy démon llap Velikosti kontejneru démona LLAP jsou nastaveny na maximální šířku uzlu, takže výsledkem bude jeden démon LLAP na každém uzlu.

:::image type="content" source="./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png " alt-text="' Počet uzlů pro procesy démon LLAP '" border="true":::

Doporučuje se, aby obě hodnoty byly stejné jako počet pracovních uzlů v clusteru interaktivních dotazů.

### <a name="considerations-for-workload-management"></a>**Požadavky na správu úloh**  
Pokud chcete povolit správu úloh pro LLAP, ujistěte se, že jste si vyhradi dostatečnou kapacitu pro fungování správy úloh podle očekávání. Správa úloh vyžaduje konfiguraci vlastní fronty PŘÍZe, která je navíc k `llap` zařazení do fronty. V souladu s požadavky na úlohy je třeba rozdělit celkovou kapacitu prostředků clusteru mezi frontu llap a frontu správy úloh.
Při aktivaci plánu prostředku seřídí Správa úloh tez hlavní aplikační servery (tez AMs).
Poznámka:

* Tez AMs vytvořená aktivací plánu prostředků spotřebovává prostředky z fronty pro správu úloh, jak je Určuje `hive.server2.tez.interactive.queue` .  
* Počet tez AMs závisí na hodnotě `QUERY_PARALLELISM` zadané v plánu prostředků.  
* Jakmile je Správa úloh aktivní, tez AMs ve frontě llap se nepoužije. Pro koordinaci dotazů se používá jenom tez AMs z fronty pro správu úloh. Tez AMs ve `llap` frontě se používá v případě, že je Správa úloh zakázaná.
 
Například: Celková kapacita clusteru = 100 GB paměti, dělená mezi LLAPí, správu úloh a výchozí fronty následujícím způsobem:
 - kapacita fronty llap = 70 GB
 - Kapacita fronty správy úloh = 20 GB
 - Výchozí kapacita fronty = 10 GB

S 20 GB v kapacitě fronty správy zatížení může plán prostředků zadat `QUERY_PARALLELISM` hodnotu jako pět, což znamená, že správa úloh může spustit pět tez AMs s velikostí kontejneru 4 GB. Pokud `QUERY_PARALLELISM` je větší než kapacita, může se stát, že některé tez AMs ve stavu přestanou reagovat `ACCEPTED` . Hiveserver2 Interactive nemůže odeslat fragmenty dotazu do tez AMs, které nejsou ve `RUNNING` stavu.


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