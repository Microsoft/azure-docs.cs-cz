---
title: Automatické škálování clusterů Azure HDInsight
description: Pomocí funkce automatického škálování můžete automaticky škálovat clustery Azure HDInsight na základě plánu nebo metriky výkonu.
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.date: 12/14/2020
ms.openlocfilehash: 5dabae76308f32da7968d8cfa89b95f1eb19c142
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863764"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Automatické škálování clusterů Azure HDInsight

Bezplatná funkce automatického škálování služby Azure HDInsight může automaticky zvýšit nebo snížit počet pracovních uzlů v clusteru na základě dříve nastavených kritérií. Funkce automatického škálování funguje tak, že škáluje počet uzlů v rámci přednastavených omezení na základě metriky výkonu nebo plánu operací horizontálního navýšení kapacity a horizontálního navýšení kapacity.

## <a name="how-it-works"></a>Jak to funguje

Funkce automatického škálování používá ke spuštění událostí škálování dva typy podmínek: prahové hodnoty pro různé metriky výkonu clusteru (nazývané *škálování na základě zatížení*) a aktivační události založené na čase (nazývané *škálování na základě plánu*). Škálování na základě zatížení mění počet uzlů v clusteru v rozsahu, který jste nastavili, k zajištění optimálního využití procesoru a minimalizaci průběžných nákladů. Škálování na základě plánu mění počet uzlů v clusteru na základě plánu operací horizontálního škálování a horizontálního navýšení kapacity.

Následující video poskytuje přehled výzev, které automatické škálování řeší a jak vám může pomáhat s řízením nákladů pomocí služby HDInsight.

> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Výběr škálování na základě zatížení nebo podle plánu

Při volbě typu škálování Vezměte v úvahu následující faktory:

* Odchylka zatížení: provede zatížení clusteru v určitých časech podle konzistentního vzoru v určitých dnech? V takovém případě je plánování na základě zatížení lepší volbou.
* Požadavky smlouvy SLA: škálování automatického škálování se znovu aktivuje místo prediktivního. Bude docházet ke zpoždění mezi tím, kdy se zatížení začne zvyšovat a v případě, že cluster potřebuje mít cílovou velikost? Pokud existují přísné požadavky smlouvy SLA a zatížení je pevným známým vzorem, je lepší volbou plánování na základě plánu.

### <a name="cluster-metrics"></a>Metriky clusteru

Automatické škálování průběžně monitoruje cluster a shromažďuje následující metriky:

|Metric|Popis|
|---|---|
|Celkový počet vyřízených PROCESORů|Celkový počet jader potřebných ke spuštění provádění všech nevyřízených kontejnerů.|
|Celkový počet nevyřízených paměti|Celková paměť (v MB) požadovaná k zahájení provádění všech kontejnerů, které čekají na zpracování.|
|Celkový bezplatný procesor|Součet všech nevyužitých jader v aktivních pracovních uzlech.|
|Celková volná paměť|Součet nevyužité paměti (v MB) na aktivních pracovních uzlech.|
|Využitá paměť na uzel|Zatížení pracovního uzlu. Pracovní uzel, na kterém se používá 10 GB paměti, se považuje za větší zatížení než pracovní proces se 2 GB využité paměti.|
|Počet hlavních serverů aplikací na uzel|Počet kontejnerů hlavních aplikací, které jsou spuštěny v pracovním uzlu. Pracovní uzel, který je hostitelem dvou kontejnerů AM, je považován za důležitější než pracovní uzel, který je hostitelem kontejnerů s nulovou hodnotou.|

Výše uvedené metriky se kontrolují každých 60 sekund. Operace škálování pro váš cluster můžete nastavit pomocí kterékoli z těchto metrik.

### <a name="load-based-scale-conditions"></a>Podmínky škálování založené na zatížení

Při zjištění následujících podmínek bude automatické škálování vydávat požadavek na škálování:

|Vertikální navýšení kapacity|Horizontální navýšení kapacity|
|---|---|
|Celkový počet vyřízených PROCESORů je větší než 3 minuty, ale celkový počet volných PROCESORů.|Celkový počet nevyřízených PROCESORů je menší než celkový bezplatný procesor po dobu více než 10 minut.|
|Celkový počet nevyřízených paměti je větší než celková volná paměť po dobu více než 3 minut.|Celková paměť, která čeká na vyřízení, je menší než celková volná paměť po dobu více než 10 minut.|

V případě škálování se při automatickém škálování vydává požadavek na horizontální navýšení kapacity, aby se přidal požadovaný počet uzlů. Horizontální navýšení kapacity vychází z toho, kolik nových pracovních uzlů je potřeba pro splnění aktuálních požadavků na procesor a paměť.

Pro horizontální navýšení kapacity vydává automatické škálování požadavek na odebrání určitého počtu uzlů. Horizontální navýšení kapacity vychází z počtu kontejnerů AM na uzel. A aktuální požadavky na procesor a paměť. Služba také detekuje, které uzly jsou kandidáty na odebrání na základě aktuálního spuštění úlohy. Operace horizontálního snížení kapacity nejprve vyřadí uzly z provozu a pak je z clusteru odebere.

### <a name="cluster-compatibility"></a>Kompatibilita clusteru

> [!Important]
> Funkce automatického škálování Azure HDInsight se 7. listopadu 2019 vydala ve fázi obecné dostupnosti pro clustery Spark a Hadoop a zahrnovala vylepšení, která nebyla k dispozici ve verzi Preview této funkce. Pokud jste vytvořili cluster Spark před 7. listopadem 2019 a chcete ve svém clusteru využívat funkci automatického škálování, doporučujeme vytvořit nový cluster a povolit v něm automatické škálování.
>
> Automatické škálování pro interaktivní dotaz (LLAP) bylo vydáno pro obecnou dostupnost pro HDI 4,0 na 27 2020. srpna. Clustery jsou stále ve verzi Preview. Automatické škálování je k dispozici pouze v clusterech Spark, Hadoop, Interactive Query a HBase.

Následující tabulka popisuje typy clusterů a verze, které jsou kompatibilní s funkcí automatického škálování.

| Verze | Spark | Hive | Interaktivní dotaz | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 bez protokolu ESP | Yes | Yes | Yes | Ano* | No | No | No |
| HDInsight 4,0 bez protokolu ESP | Yes | Yes | Yes | Ano* | No | No | No |
| HDInsight 3,6 s ESP | Yes | Yes | Yes | Ano* | No | No | No |
| HDInsight 4,0 s ESP | Yes | Yes | Yes | Ano* | No | No | No |

\* Clustery clusterů se dají konfigurovat jenom pro škálování na základě plánu, nikoli na základě zatížení.

## <a name="get-started"></a>Začínáme

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním na základě zatížení

Pokud chcete funkci automatického škálování povolit s škálováním na základě zatížení, proveďte v rámci normálního procesu vytváření clusteru následující kroky:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování** .
1. V části **typ automatického škálování** vyberte **Load-based** .
1. Zadejte zamýšlené hodnoty pro následující vlastnosti:  

    * Počáteční **počet uzlů** pro **pracovní uzel**
    * **Minimální** počet pracovních uzlů.
    * **Maximální** počet pracovních uzlů.

    :::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png" alt-text="Povolit automatické škálování na základě zatížení pracovních uzlů":::

Počáteční počet uzlů pracovního procesu musí být mezi minimální a maximální (včetně). Tato hodnota určuje počáteční velikost clusteru při jeho vytvoření. Minimální počet uzlů pracovního procesu by měl být nastavený na tři nebo víc. Škálování clusteru na méně než tři uzly může vést k zablokování v bezpečném režimu z důvodu nedostatečné replikace souborů.  Další informace najdete v tématu [získání zablokování v bezpečném režimu](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Vytvoření clusteru s automatickým škálováním na základě plánu

Pokud chcete funkci automatického škálování povolit s škálováním na základě plánu, proveďte v rámci normálního procesu vytváření clusteru následující kroky:

1. Na kartě **Konfigurace + ceny** zaškrtněte políčko **Povolit automatické škálování** .
1. Zadejte **počet uzlů** pro **pracovní uzel**, který určuje limit pro škálování clusteru.
1. V části **typ automatického škálování** vyberte možnost **plán – based** .
1. Vyberte **Konfigurovat** a otevřete tak okno **Konfigurace automatického škálování** .
1. Vyberte své časové pásmo a pak klikněte na **+ Přidat podmínku** .
1. Vyberte dny v týdnu, na které se má nová podmínka vztahovat.
1. Upravte čas uplatnění podmínky a počet uzlů, na které se má cluster škálovat.
1. V případě potřeby přidejte další podmínky.

    :::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png" alt-text="Povolit vytváření na základě plánu pracovních uzlů":::

Počet uzlů musí být mezi 3 a maximálním počtem pracovních uzlů, které jste zadali před přidáním podmínek.

### <a name="final-creation-steps"></a>Kroky konečného vytvoření

Vyberte typ virtuálního počítače pro pracovní uzly tak, že v rozevíracím seznamu v části **Velikost uzlu** vyberete virtuální počítač. Po výběru typu virtuálního počítače pro každý typ uzlu můžete zobrazit odhadované rozsahy nákladů pro celý cluster. Upravte typy virtuálních počítačů tak, aby odpovídaly vašemu rozpočtu.

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png" alt-text="Povolit velikost uzlu automatického škálování na základě plánu pracovního uzlu":::

Vaše předplatné má kvótu kapacity pro každou oblast. Celkový počet jader hlavních uzlů a maximální počet pracovních uzlů nepřekračuje kvótu kapacity. Tato kvóta je ale měkké omezení; kdykoli můžete vytvořit lístek podpory, abyste ho mohli snadno zvýšit.

> [!Note]  
> Pokud překročíte celkový počet základních kvót, zobrazí se chybová zpráva s oznámením, že maximální uzel překročil dostupné jádra v této oblasti, zvolte prosím jinou oblast, nebo požádejte podporu o zvýšení kvóty.

Další informace o vytváření clusteru HDInsight pomocí Azure Portal najdete v tématu [Vytvoření clusterů se systémem Linux v HDInsight pomocí Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Vytvoření clusteru s Správce prostředků šablonou

#### <a name="load-based-autoscaling"></a>Automatické škálování na základě zatížení

Cluster HDInsight s automatickým škálováním na základě zatížení můžete vytvořit pomocí šablony Azure Resource Manager přidáním `autoscale` uzlu do `computeProfile`  >  `workernode` oddílu s vlastnostmi, `minInstanceCount` `maxInstanceCount` jak je znázorněno v následujícím fragmentu kódu JSON. Úplnou šablonu Správce prostředků najdete v tématu [Šablona pro rychlý Start: nasazení clusteru Spark s povoleným autoškálováním na základě zatížení](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Automatické škálování na základě plánu

Cluster HDInsight s automatickým škálováním na základě plánu můžete vytvořit pomocí šablony Azure Resource Manager přidáním `autoscale` uzlu do `computeProfile`  >  `workernode` oddílu. `autoscale`Uzel obsahuje a `recurrence` , který `timezone` `schedule` popisuje, kdy bude provedeno provedení změny. Úplnou šablonu Správce prostředků najdete v tématu [nasazení clusteru Spark s povoleným autoškálováním na základě plánu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Povolit a zakázat automatické škálování běžícího clusteru

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Pokud chcete povolit automatické škálování na běžícím clusteru, vyberte v části **Nastavení** **Velikost clusteru** . Pak vyberte **Povolit automatické škálování**. Vyberte typ automatického škálování, který chcete, a zadejte možnosti pro škálování na základě zatížení nebo na základě plánu. Nakonec vyberte **Uložit**.

:::image type="content" source="./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png" alt-text="Povolit automatické škálování na základě plánu pracovních uzlů v clusteru":::

#### <a name="using-the-rest-api"></a>Použití rozhraní REST API

Pokud chcete povolit nebo zakázat automatické škálování na běžícím clusteru pomocí REST API, vytvořte požadavek POST na koncový bod automatického škálování:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Použijte příslušné parametry v datové části požadavku. K povolení automatického škálování můžete použít datovou část JSON níže. `{autoscale: null}`K zakázání automatického škálování použijte datovou část.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Úplný popis všech parametrů datové části najdete v předchozím oddílu věnovaném [Povolení automatického škálování na základě zatížení](#load-based-autoscaling) .

## <a name="monitoring-autoscale-activities"></a>Monitorování aktivit automatického škálování

### <a name="cluster-status"></a>Stav clusteru

Stav clusteru uvedený v Azure Portal vám může pomáhat monitorovat aktivity automatického škálování.

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png" alt-text="Povolit stav clusteru automatického škálování na základě zatížení pracovního uzlu":::

Všechny stavové zprávy clusteru, které se mohou zobrazit, jsou vysvětleny v následujícím seznamu.

| Stav clusteru | Description |
|---|---|
| Spuštěno | Cluster pracuje normálně. Všechny předchozí aktivity automatického škálování se úspěšně dokončily. |
| Aktualizace  | Aktualizuje se konfigurace automatického škálování clusteru.  |
| Konfigurace HDInsight  | Probíhá operace škálování a škálování clusteru.  |
| Chyba aktualizace  | HDInsight během aktualizace konfigurace automatického škálování splnila problémy. Zákazníci si můžou zvolit, že se má znovu aktualizovat nebo zakázat automatické škálování.  |
| Chyba  | S clusterem je něco špatného a nedá se použít. Odstraňte tento cluster a vytvořte nový.  |

Pokud chcete zobrazit aktuální počet uzlů v clusteru, na stránce **Přehled** pro váš cluster použijte graf **velikosti clusteru** . Nebo v části **Nastavení** vyberte **Velikost clusteru** .

### <a name="operation-history"></a>Historie operací

Historii škálování a škálování clusteru můžete zobrazit v rámci metriky clusteru. Můžete také zobrazit seznam všech akcí škálování za poslední den, týden nebo jiné časové období.

V části **monitorování** vyberte **metriky** . Pak v rozevíracím seznamu **metrika** vyberte **Přidat metriku** a **Počet aktivních pracovníků** . Chcete-li změnit časový rozsah, vyberte tlačítko v pravém horním rohu.

:::image type="content" source="./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png" alt-text="Povolit metriku automatického škálování na základě plánu pracovního uzlu":::

## <a name="best-practices"></a>Osvědčené postupy

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Zvažte latenci operací horizontálního navýšení kapacity a horizontálního navýšení kapacity.

Dokončení operace škálování může trvat 10 až 20 minut. Při nastavování přizpůsobeného plánu Naplánujte tuto prodlevu. Pokud například potřebujete, aby cluster byl 20 v 9:00 ráno, nastavte aktivační událost plánovače na dřívější čas, například 8:30 dop. to znamená, že operace škálování byla dokončena pomocí 9:00.

### <a name="prepare-for-scaling-down"></a>Příprava na horizontální navýšení kapacity

Během procesu horizontálního navýšení kapacity clusteru vyřadí automatické škálování uzly, aby splňovaly cílovou velikost. Pokud úlohy běží na těchto uzlech, automatické škálování počká, až se úlohy dokončí pro clustery Spark a Hadoop. Vzhledem k tomu, že každý pracovní uzel také slouží jako role v HDFS, jsou dočasná data přesunuta do zbývajících uzlů. Ujistěte se, že na zbývajících uzlech je dostatek místa pro hostování všech dočasných dat.

Spuštěné úlohy budou pokračovat. Čekající úlohy budou čekat na plánování s menším počtem dostupných pracovních uzlů.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Mějte na paměti, že minimální velikost clusteru

Nezmenšujte svůj cluster dolů na méně než tři uzly. Škálování clusteru na méně než tři uzly může vést k zablokování v bezpečném režimu z důvodu nedostatečné replikace souborů. Další informace najdete v tématu [získání zablokování v bezpečném režimu](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Zvýšení počtu mapovačů a reduktorů

Automatické škálování pro clustery Hadoop také sleduje použití HDFS. Pokud je HDFS zaneprázdněný, předpokládá se, že cluster stále potřebuje aktuální prostředky. V případě, že je v dotazu zapojená obrovský data, můžete zvýšit počet reduktorů a zvýšit tak paralelismus a zrychlit operace HDFS. Tímto způsobem se aktivuje správné škálování, pokud jsou k dispozici další prostředky. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Nastavení maximálního počtu souběžných dotazů konfigurace podregistru pro scénář použití ve špičce

Události automatického škálování nemění *maximální počet souběžných dotazů* konfigurace podregistru v Ambari. To znamená, že interaktivní služba pro podregistr Server 2 může v jakémkoli časovém okamžiku zpracovávat pouze daný počet souběžných dotazů, a to i v případě, že se počet procesů démona interaktivních dotazů škáluje nahoru a dolů na základě zatížení a plánu. Obecným doporučením je nastavení této konfigurace pro scénář špičky využití, aby nedocházelo k ručnímu zásahu.

Pokud je ale jen malý počet pracovních uzlů a hodnota maximálního počtu souběžných dotazů je nakonfigurovaná příliš vysoká, může se stát, že dojde k selhání restartování serveru pro podregistr 2. Minimálně potřebujete minimální počet pracovních uzlů, které můžou vyhovovat danému počtu tez AMS (rovnají se maximálnímu počtu současných souběžných dotazů). 

## <a name="limitations"></a>Omezení

### <a name="node-label-file-missing"></a>Chybí soubor popisku uzlu.

Automatické škálování HDInsight používá soubor popisků uzlů k určení, jestli je uzel připravený k provádění úloh. Soubor popisku uzlu je uložený v HDFS se třemi replikami. Pokud je velikost clusteru výrazně zvětšená a existuje velké množství dočasných dat, je pravděpodobné, že všechny tři repliky by mohly být vyřazeny. Pokud k tomu dojde, cluster vstoupí do stavu chyby.

### <a name="interactive-query-daemons-count"></a>Počet procesů démon interaktivního dotazu

U clusterů interaktivních dotazů s povoleným autoškálou se navíc událost automatického navýšení kapacity (v/v) škáluje nahoru a dolů počet interaktivních démonů dotazů na počet aktivních pracovních uzlů. Změna v počtu procesů démonů není v `num_llap_nodes` konfiguraci v Ambari trvalá. Pokud se služby pro podregistr restartují ručně, počet interaktivních démonů dotazů se resetuje podle konfigurace v Ambari.

Je-li služba interaktivní dotaz ručně restartována, je třeba ručně změnit `num_llap_node` konfiguraci (počet uzlů potřebných ke spuštění procesu démon interaktivního dotazu na podregistr) v části *pokročilý podregistr-Interactive-ENV* tak, aby odpovídal aktuálnímu počtu aktivních pracovních uzlů.

## <a name="next-steps"></a>Další kroky

Pokyny pro ruční škálování clusterů najdete v [pokynech pro škálování](hdinsight-scaling-best-practices.md) .
